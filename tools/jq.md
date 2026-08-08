# jq Cheat Sheet

## Overview

`jq` は JSON を整形・抽出・変換するための CLI ツールです。実務では API レスポンスの確認やログ処理でよく使います。

## When to Use

- JSON を読みやすく整形したいとき
- 特定のキーの値だけ取り出したいとき
- JSON のフィルタリングや変換をしたいとき

## Basic Syntax

```bash
jq [options] '<filter>' [file]
```

## Common Examples

```bash
# JSON を整形
jq . data.json

# 配列の要素を取り出す
jq '.items[]' data.json

# 特定キーだけ表示
jq '.user.name' data.json

# 条件に当てはまるものだけ抽出
jq '.items[] | select(.active == true)' data.json

# 配列の長さを確認
jq '.items | length' data.json
```

## Frequently Used Options

```bash
# カラー出力
jq -C . data.json

# 文字列だけ表示
jq -r '.name' data.json

# インデント付きで整形
jq '. | sort_keys' data.json

# 入力を配列に変換
jq '[.[] | .name]' data.json

# 文字列として配列を生成
jq -r '[.[] | .name] | @json' data.json

# キーごとに整形して出力
jq 'to_entries | sort_by(.key)' data.json
```

## Notes

- `jq` は JSON 変換で非常に強力です。
- パイプと組み合わせると API 確認に便利です。
- `-r` を使うと文字列として扱いやすくなります。

## Related Links

- Download: https://jqlang.github.io/jq/download/
- Install: https://jqlang.github.io/jq/download/
- License: https://github.com/jqlang/jq/blob/master/COPYING
