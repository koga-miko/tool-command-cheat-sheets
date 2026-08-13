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

## Filter Syntax Reference

`'...'` の中身は「jq フィルタ（jq query language）」と呼ばれる、jq 独自のクエリ言語です。JSON のようなデータ形式ではなく、JSON を加工するための DSL（ドメイン特化言語）で、[jaq](jaq.md) もほぼ同じ構文を実装しています。

```jq
# 恒等関数（入力をそのまま出力）
.

# フィールドアクセス
.foo
.foo.bar
.foo?              # フィールドが無くてもエラーにせず null を返す

# インデックス・スライス
.[0]
.[-1]
.[2:5]

# 配列・オブジェクトの全要素を展開
.[]

# パイプ（左の出力を右の入力に渡す）
.foo | .bar

# 配列・オブジェクトの構築
[.foo, .bar]
{ name: .foo, age: .bar }

# 条件でフィルタ
select(.active == true)

# 各要素に処理を適用
map(.price * 1.1)

# 比較・論理演算子
.age >= 18 and .active == true

# 値が null/エラーのときの代替値
.foo // "default"

# 文字列補間
"name: \(.name)"

# 変数束縛
. as $x | $x.foo + $x.bar

# 条件分岐
if .age >= 18 then "adult" else "minor" end
```

よく使う組み込み関数: `length` `keys` `has` `type` `to_entries` `from_entries` `sort_by` `group_by` `unique` `add` `range` `reduce` `foreach`

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
- Docs: https://jqlang.github.io/jq/manual/
- License: https://github.com/jqlang/jq/blob/master/COPYING
