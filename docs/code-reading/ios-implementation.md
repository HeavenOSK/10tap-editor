# iOS実装の詳細

このドキュメントでは、10tap-editorのiOS側の実装について詳細に解説します。

## 1. ファイル構成

### 1.1 主要ファイル
- `TenTapView.mm`: New Architecture (Fabric)対応のコンポーネントビュー
- `TenTapViewImpl.h`: 実装のインターフェース定義
- `TenTapViewImpl.mm`: カスタムキーボードの主要実装

## 2. TenTapView.mm

Fabricアーキテクチャに対応したビューコンポーネントを実装します。

```objc
@implementation TenTapView {
    TenTapViewImpl * _view;
}
```

### 2.1 初期化処理
```objc
- (instancetype)initWithFrame:(CGRect)frame {
  if (self = [super initWithFrame:frame]) {
    static const auto defaultProps = std::make_shared<const TenTapViewProps>();
    _props = defaultProps;
    _view = [[TenTapViewImpl alloc] init];
    _view.bridge = [RCTBridgeProvider shared].bridge;
    self.contentView = _view;
  }
  return self;
}
```

- デフォルトプロパティの設定
- TenTapViewImplのインスタンス化
- RCTBridgeの設定

### 2.2 プロパティ更新
```objc
- (void)updateProps:(Props::Shared const &)props oldProps:(Props::Shared const &)oldProps {
    const auto &oldViewProps = *std::static_pointer_cast<TenTapViewProps const>(_props);
    const auto &newViewProps = *std::static_pointer_cast<TenTapViewProps const>(props);

    // keyboardIDの更新
    if (oldViewProps.keyboardID != newViewProps.keyboardID) {
        NSString *newKeyboardID = [NSString stringWithUTF8String:newViewProps.keyboardID.c_str()];
        [_view setKeyboardID:newKeyboardID];
    }

    // その他のプロパティ更新...
}
```

## 3. TenTapViewImpl

### 3.1 プロパティ定義（TenTapViewImpl.h）
```objc
@interface TenTapViewImpl : UIView

@property (nonatomic, copy) NSNumber *inputTag;      // React Native側の入力フィールドタグ
@property (nonatomic, copy) NSString *keyboardID;    // カスタムキーボードのコンポーネント名
@property (nonatomic, copy) NSNumber *keyboardHeight; // キーボードの高さ
@property (nonatomic, copy) UIColor *rootBackground; // キーボードの背景色
@property (nonatomic, weak) RCTBridge *bridge;       // React Nativeブリッジ

@end
```

### 3.2 カスタムキーボードの実装（TenTapViewImpl.mm）

#### 3.2.1 HelperViewTemp
キーボード表示を制御するヘルパービュー：
```objc
@interface HelperViewTemp : UIView
@property (nullable, nonatomic, readwrite, strong) UIInputViewController *inputViewController;
@end

@implementation HelperViewTemp
- (BOOL)canBecomeFirstResponder {
  return YES;  // キーボード表示のために必要
}

- (BOOL)resignFirstResponder {
  BOOL rv = [super resignFirstResponder];
  [self removeFromSuperview];
  return rv;
}
@end
```

#### 3.2.2 キーボードのセットアップ
```objc
- (void)setInputTag:(NSNumber *)inputTag {
  _inputTag = inputTag;

  // ブリッジチェック
  if (self.bridge == nil) {
    NSLog(@"No bridge");
    return;
  }

  // 入力フィールドの取得
  UIView *inputField = [self.bridge.uiManager viewForReactTag:inputTag];
  if (inputField != nil) {
    // InputViewControllerの設定
    UIInputViewController *inputController = [[UIInputViewController alloc] init];
    UIInputView *inputView = [[UIInputView alloc] initWithFrame:CGRectZero inputViewStyle:UIInputViewStyleKeyboard];
    inputView.allowsSelfSizing = YES;
    inputView.translatesAutoresizingMaskIntoConstraints = NO;
    inputController.inputView = inputView;

    // キーボードのセットアップ...
  }
}
```

#### 3.2.3 React Nativeコンポーネントの組み込み
New Architecture対応：
```objc
#ifdef RCT_NEW_ARCH_ENABLED
    id appDelegate = [UIApplication sharedApplication].delegate;
    if ([appDelegate respondsToSelector:NSSelectorFromString(@"rootViewFactory")]) {
      id rootViewFactory = [appDelegate valueForKey:@"rootViewFactory"];
      SEL viewWithModuleNameSelector = NSSelectorFromString(@"viewWithModuleName:initialProperties:");
      RCTSurfaceHostingProxyRootView *hostingRootView = ...;
      customKeyboard = hostingRootView;
    }
#else
    customKeyboard = [[RCTRootView alloc] initWithBridge:self.bridge
                                             moduleName:_keyboardID
                                      initialProperties:nil];
#endif
```

#### 3.2.4 レイアウト制御
```objc
[NSLayoutConstraint activateConstraints:@[
  [customKeyboard.leadingAnchor constraintEqualToAnchor:inputController.view.leadingAnchor],
  [customKeyboard.trailingAnchor constraintEqualToAnchor:inputController.view.trailingAnchor],
  [customKeyboard.topAnchor constraintEqualToAnchor:inputController.view.topAnchor],
  [customKeyboard.bottomAnchor constraintEqualToAnchor:inputController.view.bottomAnchor],
  [customKeyboard.heightAnchor constraintEqualToConstant:[self.keyboardHeight floatValue]]
]];
```

## 4. メモリ管理

### 4.1 キーボードのクリーンアップ
```objc
- (void)cleanupKeyboard {
  if (globalCustomKeyboard) {
    [globalCustomKeyboard removeFromSuperview];
    globalCustomKeyboard = nil;
  }
}
```

### 4.2 キーボードIDの更新時の処理
```objc
- (void)setKeyboardID:(NSString *)keyboardID {
  _keyboardID = keyboardID;

  if ([_keyboardID isEqualToString:@""]) {
    [self cleanupKeyboard];
  }
}
```

## 5. 技術的な考慮事項

### 5.1 New Architecture (Fabric)対応
- `RCT_NEW_ARCH_ENABLED`マクロによる条件分岐
- FabricのSurfaceHostingProxyRootViewの使用
- コンポーネントディスクリプタの提供

### 5.2 メモリ管理
- グローバル参照の適切な管理
- キーボード非表示時のクリーンアップ
- 循環参照の防止

### 5.3 レイアウト
- Auto Layoutによる制約設定
- 動的なキーボードサイズの管理
- 適切なマージンとパディングの設定

### 5.4 パフォーマンス
- 効率的なプロパティ更新
- 必要な時のみのビューの再構築
- メモリリークの防止
