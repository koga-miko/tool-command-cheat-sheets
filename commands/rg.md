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
```

## Notes

- `.git` 配下は既定で除外されます。
- `grep` の代替として使いやすいです。
- ログ確認やコード検索で強力です。

## Related Links

- Download: https://github.com/BurntSushi/ripgrep/releases
- Install: https://github.com/BurntSushi/ripgrep/blob/master/README.md#installation
- License: https://github.com/BurntSushi/ripgrep/blob/master/LICENSE-MIT
