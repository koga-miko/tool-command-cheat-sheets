# fd Cheat Sheet

## Overview

`fd` は、rust 製の `find` 代替コマンドです。実務ではファイル名の探索や、サイズ・更新日時を含めた確認に向いています。

## When to Use

- ファイル名を高速に探したいとき
- `find` より簡潔な構文で検索したいとき
- 隠しファイルや `.gitignore` を意識しながら確認したいとき

## Basic Syntax

```bash
fd [options] [pattern] [path]
```

## Common Examples

```bash
# すべての Markdown ファイルを検索
fd "\.md$"

# 特定ディレクトリ内を検索
fd "src" ./projects

# ディレクトリだけ検索
fd -t d "cache"

# ファイルだけ検索
fd -t f "\.json$"

# 隠しファイルも含めて検索
fd -H "config"

# 更新日時とサイズを表示
fd -t f -x stat -c '%y %s %n' .

# サイズの大きいファイルを確認
fd -t f -x du -h {} | sort -h
```

## Frequently Used Options

```bash
# 隠しファイルも含める
fd -H "pattern"

# .gitignore を無視して検索
fd -I "pattern"

# ファイルタイプを限定
fd -t f "pattern"
fd -t d "pattern"

# 拡張子を指定
fd -e md -e ts "pattern"

# 実行コマンドを渡す
fd -x echo {}

# 1コマンドに複数のパスをまとめて渡す
fd -X printf '%s\n' {}
```

## Notes

- `find` より短く書けることが多いです。
- `.gitignore` を尊重するので実務で扱いやすいです。
- ファイル探索の高速化に向いています。

## Related Links

- Download: https://github.com/sharkdp/fd/releases
- Install: https://github.com/sharkdp/fd#installation
- License: https://github.com/sharkdp/fd/blob/master/LICENSE-MIT
