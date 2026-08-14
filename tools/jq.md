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

### よく使う組み込み関数

| 関数 | 意味 |
|---|---|
| `length` | 値の「長さ」（文字列: 文字数 / 配列: 要素数 / オブジェクト: キー数 / 数値: 絶対値） |
| `keys` | オブジェクトのキー一覧をソート済み配列で返す（配列なら添字の配列） |
| `has(x)` | オブジェクトがキー `x` を持つか、配列がインデックス `x` を持つかを真偽値で返す |
| `type` | 値の型を文字列で返す（`"null"` `"boolean"` `"number"` `"string"` `"array"` `"object"`） |
| `to_entries` | オブジェクトを `{key, value}` の配列に変換する |
| `from_entries` | `to_entries` の逆変換。`{key,value}` 形式の配列をオブジェクトに戻す |
| `sort_by(f)` | `f` を適用した結果を基準に配列を並び替える |
| `group_by(f)` | `f` の結果が同じ要素同士をグループ化する（結果はグループキー順にソートされる） |
| `unique` | 配列をソートしつつ重複を除去する |
| `add` | 配列の要素を `+` で畳み込む（数値は合計、文字列/配列は連結、オブジェクトはマージ、空配列は `null`） |
| `range(...)` | 数値のストリームを生成する |
| `reduce` | 式の出力を1つずつ変数に束縛しながら畳み込み、最終結果だけを返す |
| `foreach` | `reduce` と同じ畳み込みをしつつ、各ステップの中間結果も逐次出力する |

```jq
# length
"hello" | length            # => 5（文字数）
[1,2,3] | length            # => 3（要素数）
{"a":1,"b":2} | length      # => 2（キー数）

# keys
{"b":2,"a":1} | keys        # => ["a","b"]（ソート済み）

# has
{"a":1} | has("a")          # => true
[1,2,3] | has(5)            # => false（インデックス5は存在しない）

# type
[1,"a",null,true,{},[]] | map(type)
# => ["number","string","null","boolean","object","array"]

# to_entries
{"a":1,"b":2} | to_entries
# => [{"key":"a","value":1},{"key":"b","value":2}]

# from_entries（to_entries の逆変換）
[{"key":"a","value":1},{"key":"b","value":2}] | from_entries
# => {"a":1,"b":2}

# sort_by
[{"name":"b","age":30},{"name":"a","age":20}] | sort_by(.age)
# => [{"name":"a","age":20},{"name":"b","age":30}]

# group_by
[{"type":"fruit","name":"apple"},{"type":"veg","name":"carrot"},{"type":"fruit","name":"banana"}]
  | group_by(.type)
# => [[{apple,fruit},{banana,fruit}], [{carrot,veg}]]（グループの配列）

# unique
[3,1,2,1,3] | unique         # => [1,2,3]

# add
[1,2,3] | add                # => 6（合計）
["a","b","c"] | add          # => "abc"（連結）
[{"a":1},{"b":2}] | add      # => {"a":1,"b":2}（マージ）

# range
range(3)                     # => 0, 1, 2
range(2;5)                   # => 2, 3, 4
range(0;10;3)                # => 0, 3, 6, 9

# reduce: INIT から始めて、EXPR の出力ごとに UPDATE で畳み込み、最終結果だけを返す
[1,2,3,4] | reduce .[] as $x (0; . + $x)
# => 10（合計だけを出力）

# foreach: reduce と同じ畳み込みだが、途中経過も逐次出力する
[1,2,3,4] | foreach .[] as $x (0; . + $x; .)
# => 1, 3, 6, 10（累積和のストリーム）
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
- Docs: https://jqlang.github.io/jq/manual/
- License: https://github.com/jqlang/jq/blob/master/COPYING
