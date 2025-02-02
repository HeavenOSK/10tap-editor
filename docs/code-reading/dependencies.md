# 依存関係の概要

## プロジェクト情報
- **名前**: @10play/tentap-editor
- **説明**: React Native用リッチテキストエディタ
- **ライセンス**: MIT

## コア依存関係

### フレームワーク依存関係
- **react**: ^18.2.0
- **react-native**: ^0.73.2
- **react-native-webview**: ^13.7.1
- **react-dom**: ^18.2.0

### TipTapエディタ依存関係
このプロジェクトでは、TipTapをコアのリッチテキスト編集エンジンとして使用しています。以下の拡張機能が使用されています：

#### コア拡張機能
- @tiptap/extension-document - 文書構造の基本機能
- @tiptap/extension-hard-break - 改行機能
- @tiptap/starter-kit - 基本機能セット
- @tiptap/react - React連携機能

#### テキスト書式設定拡張機能
- @tiptap/extension-bold - 太字機能
- @tiptap/extension-italic - 斜体機能
- @tiptap/extension-underline - 下線機能
- @tiptap/extension-strike - 取り消し線機能
- @tiptap/extension-code - インラインコード機能
- @tiptap/extension-code-block - コードブロック機能

#### リスト拡張機能
- @tiptap/extension-bullet-list - 箇条書きリスト
- @tiptap/extension-ordered-list - 番号付きリスト
- @tiptap/extension-list-item - リストアイテム
- @tiptap/extension-task-list - タスクリスト
- @tiptap/extension-task-item - タスクアイテム

#### スタイル拡張機能
- @tiptap/extension-color - 文字色機能
- @tiptap/extension-text-style - テキストスタイル
- @tiptap/extension-highlight - ハイライト機能
- @tiptap/extension-blockquote - 引用ブロック

#### その他の拡張機能
- @tiptap/extension-heading - 見出し機能
- @tiptap/extension-image - 画像機能
- @tiptap/extension-link - リンク機能
- @tiptap/extension-dropcursor - ドロップカーソル
- @tiptap/extension-history - 履歴管理
- @tiptap/extension-placeholder - プレースホルダー
- @tiptap/extension-horizontal-rule - 水平線

### ユーティリティ依存関係
- **lodash**: ^4.17.21 - 汎用ユーティリティライブラリ

## 開発用依存関係

### ビルドツール
- **vite**: ^5.0.12 - ビルドツールと開発サーバー
- **@vitejs/plugin-react**: ^4.2.1 - ViteのReactプラグイン
- **vite-plugin-singlefile**: ^1.0.0 - 単一ファイルバンドル用プラグイン
- **react-native-builder-bob**: ^0.20.0 - React Nativeライブラリビルドツール
- **rimraf**: ^5.0.5 - クロスプラットフォームrm -rf
- **turbo**: ^1.12.2 - モノレポビルドシステム

### TypeScriptと型定義
- **typescript**: ^5.0.2
- **@types/react**: ~17.0.21
- **@types/react-dom**: ^18.2.18
- **@types/react-native**: 0.70.0
- **@types/lodash**: ^4.14.202
- **@types/jest**: ^28.1.2

### コード品質ツール
- **eslint**: ^8.4.1 - コード品質チェック
- **prettier**: ^2.0.5 - コードフォーマッター
- **jest**: ^28.1.1 - テストフレームワーク
- **@commitlint/config-conventional**: ^17.0.2 - コミットメッセージ検証
- **@evilmartians/lefthook**: ^1.5.0 - Gitフック管理

## プロジェクト構造

### ビルド出力
プロジェクトは異なる環境向けに複数の形式でビルドを生成します：

```
lib/
├── commonjs/     # CommonJS形式
├── module/       # ES Modules形式
└── typescript/   # TypeScript型定義

lib-web/         # Web専用ビルド
```

### ソースコード構成
```
src/
├── RichText/           # エディタのコア実装
├── bridges/           # TipTap連携機能
├── simpleWebEditor/   # Webエディタ実装
├── webEditorUtils/    # Webユーティリティ
└── types/            # TypeScript型定義
```

### プラットフォーム固有のコード
```
android/          # Androidネイティブ実装
ios/              # iOSネイティブ実装
```

### サンプルとドキュメント
```
example/          # サンプルReact Nativeアプリ
example76/        # 追加のサンプルアプリ
website/          # ドキュメントサイト
```

## 環境要件
- **Node.js**: >= 18.0.0
- **パッケージマネージャ**: yarn@3.6.1

## ビルド設定

### TypeScript設定
主要なTypeScriptコンパイラオプション：
- ターゲット: ESNext
- モジュール: ESNext
- JSX: React
- 厳格モード有効
- パスエイリアス設定によるクリーンなインポート

### Vite設定
開発サーバー設定：
- ポート: 3000
- ルート: src/simpleWebEditor
- プラグイン: react, singlefile

### ワークスペース
プロジェクトは以下のパッケージを管理するyarnワークスペースを使用：
- example - メインのサンプルアプリ
- example76 - 追加のサンプルアプリ
- website - ドキュメントサイト
