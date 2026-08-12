# Tool and Command Cheat Sheets

このリポジトリは、実務でよく使う CLI コマンド・ツールのチートシートをまとめるための資料集です。  
1シート = 1つのコマンドまたはツールとして、すぐ見て使える構成にしています。

## 使い方

- まずは目次から気になるコマンドやツールを選んでください。
- 各ページは「概要」「使いどころ」「基本構文」「実務例」「よく使うオプション」「補足」「関連リンク」の順で読めるようにしています。
- すぐに使いたい場合は、各ページの「Common Examples」から確認できます。

## 公開に向けた方針

- 参考資料として整理した内容を公開しています。
- 公式情報やドキュメントを参照しながら、実務で使いやすい形にまとめています。
- 可能な限り引用元や関連リンクを明記しています。

## ライセンス

このリポジトリの内容は、必要に応じて再利用・修正できます。詳細は [LICENSE](LICENSE) を参照してください。

## 分け方の基準

このリポジトリでは、次のような基準で「コマンド」と「ツール」を分けています。

- コマンド: シェルで日常的に使う汎用的な CLI です。パイプやリダイレクト、スクリプト化と相性がよいです。
  - 例: rg, fd, git, curl, docker, coreutils
- ツール: 特定の領域に特化したユーティリティです。データ処理・メディア処理・バイナリ確認などに向いています。
  - 例: frawk, ffmpeg, jq, jaq, hexyl
- データ形式: CLI ではなく、設定ファイルやデータのやり取りで使う記述フォーマットそのものです。構文や書き方のリファレンスとして使います。
  - 例: JSON, YAML, TOML

## 目次

### コマンド

- [rg](commands/rg.md) - 高速なファイル内容検索
- [fd](commands/fd.md) - rust 製の find 代替
- [git](commands/git.md) - バージョン管理
- [curl](commands/curl.md) - HTTP リクエスト
- [docker](commands/docker.md) - コンテナ操作
- [coreutils](commands/coreutils.md) - 基本ファイル操作の共通コマンド

### ツール

- [frawk](tools/frawk.md) - 行単位のテキスト処理
- [ffmpeg](tools/ffmpeg.md) - 音声・動画の変換と抽出
- [jq](tools/jq.md) - JSON の整形と抽出
- [jaq](tools/jaq.md) - Rust 製の高速な jq 互換 CLI
- [hexyl](tools/hexyl.md) - バイナリの16進表示

### データ形式

- [JSON](formats/json.md) - 軽量なデータ交換フォーマット
- [YAML](formats/yaml.md) - インデントで構造を表す設定ファイル向けフォーマット
- [TOML](formats/toml.md) - キーと値を中心としたシンプルな設定ファイル向けフォーマット

## 1シートの構成

各シートは、以下の見出しで統一しています。

1. Overview
2. When to Use
3. Basic Syntax
4. Common Examples
5. Frequently Used Options
6. Notes
7. Related Links

各ページでは、実務でよく使う例を優先して載せ、オプションはそのままコピペできる形にしています。

## 実務向けの方針

- すぐ使える実例を優先する
- 重要なオプションはそのままコピペできる形で載せる
- 公式ページのダウンロード・インストール・ライセンス情報を併記する
- いきなり全部を覚えるより、よく使うものから順に使う前提で整理する

## 推奨の読み方

### よく使う順

- 検索系: rg / fd / coreutils
- 開発作業: git / curl / docker
- データ処理: jq / jaq / frawk / hexyl
- メディア処理: ffmpeg
- データ形式: JSON / YAML / TOML

### 学習の順序

1. まずは検索系: rg / fd / coreutils
2. 次に開発作業: git / curl / docker
3. その後にデータ処理: jq / jaq / frawk / hexyl
4. 最後にメディア変換: ffmpeg
5. 合わせてデータ形式の基礎: JSON / YAML / TOML
