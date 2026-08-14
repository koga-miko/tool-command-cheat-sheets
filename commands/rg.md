# rg Cheat Sheet

## Overview

`rg` は、ripgrep の略で、ファイル内容を高速に検索するためのコマンドです。実務ではコード検索やログ確認でよく使います。

## When to Use

- 文字列を検索したいとき
- 特定のファイルだけを絞って確認したいとき
- ログや設定ファイルの内容を追いかけたいとき

## Basic Syntax

```bash
rg [options] <pattern> [path]
```

## Common Examples

```bash
# 文字列を再帰検索
rg "TODO" .

# 行番号付きで表示
rg -n "function" src/

# 大文字小文字を無視して検索
rg -i "error|warn" logs/

# マッチしたファイル名だけ表示
rg -l "TODO" .

# 特定の拡張子だけ検索
rg --glob "*.ts" "class" src/

# コンテキスト付きで表示
rg -n -C 2 "TODO" src/

# dist や node_modules を除外して検索
rg -g '!dist' -g '!node_modules' "TODO" .

# ファイルタイプを指定して検索（拡張子を意識しなくてよい）
rg -t py "def main"

# ファイルタイプを除外して検索
rg -T test "TODO" .

# 単語単位で一致（部分一致を除外）
rg -w "id" src/

# 一致しない行を表示
rg -v "TODO" src/

# 正規表現を使わずリテラル文字列として検索
rg -F "a.b[c]" src/
```

## Frequently Used Options

```bash
# 行番号を表示
rg -n "pattern" .

# 大文字小文字を無視
rg -i "pattern" .

# 一致したファイル名だけ表示
rg -l "pattern" .

# 隠しファイルも含めて検索
rg --hidden "pattern" .

# 特定パターンを除外
rg -g '!dist' "pattern" .

# 前後 2 行を表示
rg -C 2 "pattern" .

# 1 行ずつ確認しやすくする
rg -n --color=always "pattern" .

# 一致数だけカウント
rg -c "pattern" .

# 一致した部分だけ表示
rg -o "pattern" .

# ファイル一覧のみ取得（検索対象の確認）
rg --files

# サポートされているファイルタイプ一覧を確認
rg --type-list

# 検索結果の統計情報を表示
rg --stats "pattern" .

# JSON 形式で出力（他ツールとの連携向け）
rg --json "pattern" . | jq

# ターミナルでもファイル名を毎行フラットに表示する（既定はファイルごとに見出しでグループ化）
rg -n --no-heading "pattern" .

# エディタ連携向け: 常に「ファイル名:行番号:列番号:内容」を1マッチ1行で出す
rg --vimgrep "pattern" .
```

## Notes

- `.git` 配下は既定で除外されます。
- `grep` の代替として使いやすいです。
- ログ確認やコード検索で強力です。
- `-t`/`-T` で拡張子を意識せずファイルタイプを指定・除外できます（`rg --type-list` で一覧確認可）。
- `-w` は部分一致を避けたいとき（例: `id` で `valid` にヒットさせたくない場合）に便利です。
- `-n` はターミナルに直接出力すると「ファイルごとに見出し＋行番号:内容」のヘッディング形式になり、ファイル名が毎行には出ません。毎行に `ファイル名:行番号:内容` を出したい場合は `--no-heading` を付けます（パイプに繋いだ場合は自動的にこの形式になります）。ファイル名と行番号だけ欲しい場合は `rg -n "pattern" | cut -d: -f1,2` のように後段で加工します。

## Related Links

- Download: https://github.com/BurntSushi/ripgrep/releases
- Install: https://github.com/BurntSushi/ripgrep/blob/master/README.md#installation
- Docs: https://github.com/BurntSushi/ripgrep/blob/master/GUIDE.md
- License: https://github.com/BurntSushi/ripgrep/blob/master/LICENSE-MIT
