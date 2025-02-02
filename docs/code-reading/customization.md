# TenTapのカスタマイズ機能

TenTapは、エディタの見た目や動作を柔軟にカスタマイズできる機能を提供しています。本ドキュメントでは、特にスタイリングに関するカスタマイズ機能について解説します。

## カスタマイズの主要な方法

### 1. テーマによるカスタマイズ

TenTapは包括的なテーマシステムを提供しており、以下の3つの主要な部分をカスタマイズできます：

#### 1.1 ツールバーのテーマ（ToolbarTheme）
- ツールバーの背景色、ボーダー
- アイコンの色、サイズ
- ボタンのスタイル
- リンク入力バーのスタイル

```typescript
export type ToolbarTheme = {
  toolbarBody: StyleProp<ViewStyle>;
  toolbarButton: StyleProp<ViewStyle>;
  iconDisabled: StyleProp<ImageStyle>;
  iconActive: StyleProp<ImageStyle>;
  icon: StyleProp<ImageStyle>;
  iconWrapper: StyleProp<ViewStyle>;
  iconWrapperDisabled: StyleProp<ViewStyle>;
  iconWrapperActive: StyleProp<ViewStyle>;
  hidden: StyleProp<ViewStyle>;
  keyboardAvoidingView: StyleProp<ViewStyle>;
  linkBarTheme: LinkBarTheme;
};
```

#### 1.2 カラーキーボードのテーマ（ColorKeyboardTheme）
- キーボードの背景色
- カラーボタンのスタイル
- テキストの色
- カラーパレットのカスタマイズ

```typescript
export type ColorKeyboardTheme = {
  scrollViewContainer: StyleProp<ViewStyle>;
  keyboardContainer: StyleProp<ViewStyle>;
  colorRow: StyleProp<ViewStyle>;
  colorButton: StyleProp<ViewStyle>;
  activeButton: StyleProp<ViewStyle>;
  iconContainer: StyleProp<ViewStyle>;
  textIcon: StyleProp<ImageStyle>;
  highlight: StyleProp<ViewStyle>;
  colorText: StyleProp<TextStyle>;
  sectionTitle: StyleProp<TextStyle>;
  bottomSpacer: StyleProp<ViewStyle>;
  colorSelection: Color[];
  highlightSelection: Color[];
  keyboardRootColor: ColorValue;
};
```

#### 1.3 エディタ本体のテーマ（EditorTheme）
- Webviewの背景色
- コンテナのスタイル

```typescript
export interface EditorTheme {
  toolbar: ToolbarTheme;
  colorKeyboard: ColorKeyboardTheme;
  webview: StyleProp<ViewStyle>;
  webviewContainer: StyleProp<ViewStyle>;
}
```

### 2. CSSによるカスタマイズ

エディタのコンテンツ自体は以下の方法でカスタマイズ可能です：

#### 2.1 CoreBridge.configureCSS()による基本スタイルの設定
```typescript
const editor = useEditorBridge({
  bridgeExtensions: [
    ...TenTapStartKit,
    CoreBridge.configureCSS(customCss),
  ],
});
```

#### 2.2 動的なCSS注入
```typescript
editor.injectCSS(
  `
  * {
    font-size: ${fontSize}px;
  }
  `,
  'font-size' // タグ名
);
```

#### 2.3 特定の要素に対する個別のスタイル設定
```typescript
const customCodeBlockCSS = `
code {
    background-color: #ffdede;
    border-radius: 0.25em;
    border-color: #e45d5d;
    border-width: 1px;
    border-style: solid;
    color: #cd4242;
}
`;

const editor = useEditorBridge({
  bridgeExtensions: [
    ...TenTapStartKit,
    CodeBridge.configureCSS(customCodeBlockCSS),
  ],
});
```

## 実装例

### ダークテーマの実装
```typescript
const editor = useEditorBridge({
  bridgeExtensions: [
    ...TenTapStartKit,
    CoreBridge.configureCSS(darkEditorCss),
  ],
  theme: darkEditorTheme,
});
```

### カスタムフォントの適用
```typescript
const customFont = `
${ProtestRiotFont}
* {
    font-family: 'Protest Riot', sans-serif;
}
`;

const editor = useEditorBridge({
  bridgeExtensions: [
    ...TenTapStartKit,
    CoreBridge.configureCSS(customFont),
  ],
});
```

## 特徴的な点

1. **型安全なテーマシステム**
   - TypeScriptの型システムを活用し、テーマのプロパティに対して型チェックが行われます

2. **ダークモードのビルトインサポート**
   - デフォルトで提供される`darkEditorTheme`と`darkEditorCss`を使用して簡単にダークモードを実装できます

3. **動的なスタイル更新**
   - `injectCSS`メソッドを使用して、実行時にスタイルを動的に更新できます

4. **モジュラー式のカスタマイズ**
   - 各要素（コードブロック、リンク、テキストなど）に対して個別にスタイルを設定できます
   - 必要な部分だけを選択的にカスタマイズ可能です
