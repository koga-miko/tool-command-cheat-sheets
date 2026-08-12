# TOML Cheat Sheet

## Overview

TOML（Tom's Obvious, Minimal Language）は、キーと値を中心としたシンプルな設定ファイル向けフォーマットです。実務では `Cargo.toml`（Rust）や `pyproject.toml`（Python）など、ツールの設定ファイルとしてよく使われます。

## When to Use

- Rust（Cargo）や Python（pyproject.toml）などのプロジェクト設定を書きたいとき
- コメント付きで人間が読み書きしやすい設定ファイルにしたいとき
- ネストした設定を階層（テーブル）で整理したいとき

## Basic Syntax

```toml
key = "value"
number = 1
flag = true

[table]
key = "value"

[[array_of_tables]]
key = "value"
```

## Common Examples

```toml
# 基本のキーと値
title = "demo"
version = 1

# 文字列
name = "example"
path = 'C:\Users\demo'  # リテラル文字列（エスケープ不要）

# 配列
tags = ["cli", "tool", "cheatsheet"]

# テーブル（オブジェクト相当）
[user]
name = "demo"
age = 20

# ネストしたテーブル
[user.address]
city = "Tokyo"

# テーブルの配列
[[servers]]
host = "a.example.com"

[[servers]]
host = "b.example.com"

# インラインテーブル
point = { x = 1, y = 2 }

# 日時
created_at = 2026-08-12T10:00:00Z
```

## Frequently Used Options

```toml
# コメント
key = "value" # ここがコメント

# ドット区切りキー（テーブルを省略して1行で書く）
database.host = "localhost"
database.port = 5432

# 複数行文字列
description = """
1行目
2行目
"""

# 複数行リテラル文字列（エスケープ不要）
regex = '''
\d+\.\d+
'''

# 型を混在させない配列（TOML では型が揃っている必要がある）
valid_array = [1, 2, 3]
```

## Notes

- キーは重複定義できません（同じキーを2回書くとエラーになります）。
- 配列は要素の型を統一する必要があります（数値と文字列を混ぜるのは不可）。
- `[[table]]`（二重角括弧）は「テーブルの配列」を表し、`[table]`（単一角括弧）とは意味が異なります。
- 日時型（`2026-08-12T10:00:00Z` など）をネイティブにサポートしている点が JSON や YAML と異なります。
- JSON よりコメントが書けて読みやすく、YAML よりインデント崩れに悩まされにくい、という位置づけでよく選ばれます。

## Related Links

- Spec: https://toml.io/ja/
- GitHub: https://github.com/toml-lang/toml
- Online Validator: https://www.toml-lint.com/
