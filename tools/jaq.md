# jaq Cheat Sheet

## Overview

`jaq`（発音: "jack"）は、Rust 製の `jq` 互換 CLI です。`jq` とほぼ同じフィルタ構文をそのまま使えつつ、コンパイル言語ならではの高速な実行と、分かりやすいエラー表示が特徴です。実務では大きめの JSON を繰り返し処理する場面や、`jq` フィルタのデバッグをしたい場面で向いています。また `jq` には無い機能として、JSON だけでなく YAML / TOML / XML / CBOR / CSV / TSV も直接読み書きできます（v3.0 以降）。

## When to Use

- `jq` と同じ書き方のまま、処理速度を上げたいとき
- 大きな JSON ファイルやストリームを何度も加工・集計したいとき
- フィルタの構文ミスを、どこが間違っているか分かりやすく確認したいとき
- `jq` の一部の癖のある挙動（バグ）を避けたいとき
- YAML / TOML / XML / CSV などを JSON に変換したり、逆に JSON をそれらの形式で出力したいとき

## Basic Syntax

```bash
jaq [options] '<filter>' [file]
```

## Filter Syntax Reference

`'...'` の中身は「jq フィルタ（jq query language）」と呼ばれる、jq 由来のクエリ言語です。JSON のようなデータ形式ではなく、JSON を加工するための DSL（ドメイン特化言語）で、`jaq` はこの構文を互換実装しています（詳しくは [jq](jq.md) 側の Filter Syntax Reference も参照）。

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
# => [[{"type":"fruit","name":"apple"},{"type":"fruit","name":"banana"}],[{"type":"veg","name":"carrot"}]]
# （.type の値でソートされた「グループの配列」。fruit グループが先、veg グループが後）

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
# JSON を整形（jq と同じ書き方がそのまま使える）
jaq . data.json

# 配列の要素を取り出す
jaq '.items[]' data.json

# 特定キーだけ表示
jaq '.user.name' data.json

# 条件に当てはまるものだけ抽出
jaq '.items[] | select(.active == true)' data.json

# 配列の長さを確認
jaq '.items | length' data.json

# 文字列として出力（クォートなし）
jaq -r '.name' data.json

# コンパクトな1行出力
jaq -c '.items[]' data.json
```

## Multi-Format Input/Output（jaq だけの機能）

`jq` は JSON しか扱えませんが、`jaq` は `--from FORMAT` / `--to FORMAT` を指定することで JSON 以外の形式もそのまま読み書きできます。対応フォーマットは `raw` `raw0` `json` `yaml` `cbor` `toml` `xml` `csv` `tsv` です。拡張子（`.yaml` `.yml` `.toml` `.xml` `.xhtml` `.csv` `.tsv` `.cbor`）からも自動判定されるため、多くの場合 `--from` は省略できます。

```bash
# YAML を読み込んでクエリ（--from を明示）
jaq --from yaml '.services | keys' docker-compose.yml

# 拡張子から自動判定されるので --from を省略できる
jaq '.profile.release["codegen-units"]' Cargo.toml

# YAML → JSON に変換
jaq --from yaml . config.yaml

# JSON → YAML に変換（1回のフィルタ評価で形式変換もできる）
jaq --to yaml . data.json

# TOML → JSON に変換
jaq --from toml . Cargo.toml

# JSON → TOML に変換（ルート値がオブジェクトである必要あり）
jaq --to toml '{name: .name, version: .version}' package.json

# CSV を読み込む（各行がそのまま配列になる）
jaq -c --from csv data.csv

# CSV 全体を1つの配列としてまとめて読み込む
jaq -c --from csv --slurp data.csv

# CSV のヘッダー行をキーにしたオブジェクトへ変換する
# （table.jq の from_table は1行ずつ出力するジェネレータなので [ ] で配列にまとめる）
jaq -s -L . 'include "table"; [from_table]' data.csv

# さらに {table: [...]} のようなオブジェクトの中に配列として詰める
jaq -s -L . 'include "table"; {table: [from_table]}' data.csv

# XML を読み込んでクエリ（タグは {t: 名前, a: 属性, c: 子要素} で表現される）
jaq --from xml '.. | select(.t? == "a") | .a.href' page.xhtml

# XML を読み込んで加工し、XML のまま出力する
jaq --from xml --to xml '(.. | select(.t? == "em") | .t) = "i"' page.xhtml
```

## Frequently Used Options

```bash
# 入力を使わずフィルタだけ評価（値の生成やテストに便利）
jaq -n '1 + 1'

# 複数の JSON を1つの配列にまとめて読み込む
jaq -s '.' data.json

# 文字列として読み込む（JSON として解釈しない）
jaq -R '.' lines.txt

# キーをソートして出力
jaq -S . data.json

# インデント幅を指定
jaq --indent 4 . data.json

# タブでインデント
jaq --tab . data.json

# 変数を渡す
jaq --arg name "foo" '.user.name == $name' data.json

# JSON値を変数として渡す
jaq --argjson limit 10 '.items[:$limit]' data.json

# フィルタをファイルから読み込む
jaq -f filter.jq data.json
```

## Notes

- `jq` と文法互換なので、既存の `jq` フィルタや [jq](jq.md) のチートシートの例はほぼそのまま `jaq` でも動きます。`alias jq=jaq` にして置き換える使い方もできます。
- Rust 実装のため `jq` より大幅に高速で、大きめの JSON や繰り返し実行が多いバッチ処理に向いています。
- フィルタの構文エラーがどこにあるか（該当箇所）を分かりやすく指摘してくれるため、複雑なフィルタのデバッグがしやすいです。
- `jq` の一部のバグ・不整合な挙動を修正しているため、エッジケースでは `jq` と出力が完全には一致しないことがあります。乗り換える際は重要な処理で出力を比較しておくと安心です。
- インストールは `cargo install jaq`（Rust ツールチェーンが必要）が手軽です。
- TOML への出力（`--to toml`）はルート値がオブジェクトである必要があり、配列や `null` を含むオブジェクトなどは出力に失敗します。
- XML は要素を `{"t": タグ名, "a": 属性, "c": 子要素の配列}` という TAC オブジェクトの形で表現し、コメントや CDATA、XML宣言なども情報を欠落させずに扱えます（ラウンドトリップ可能な設計）。
- CSV/TSV はヘッダー行を自動でオブジェクトのキーにはせず、各行が単純な配列になります。ヘッダー付きで扱いたい場合は公式リポジトリの `examples/table.jq`（`from_table` などのヘルパー）が参考になります。`from_table` は1行ずつオブジェクトを出力するジェネレータなので、単体で呼ぶと複数の値が並んで出力されます。`[from_table]` のように `[ ]` で囲むと1つの配列にまとめられ（`jq`/`jaq` 共通のイディオムです）、`{table: [from_table]}` のように任意のキーの下にネストさせることもできます。
- YAML の出力にはデフォルトで `---`（開始）/ `...`（終了）が付きます。複数の値をまとめて出力したい場合は `-j`（`--join-output`）で省略できます。

## Related Links

- Download: https://github.com/01mf02/jaq/releases
- Install: https://github.com/01mf02/jaq#installation
- Manual (multi-format support): https://gedenkt.at/jaq/manual/#formats
- License: https://github.com/01mf02/jaq/blob/main/LICENSE-MIT
