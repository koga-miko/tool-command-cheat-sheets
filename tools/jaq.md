# jaq Cheat Sheet

## Overview

`jaq`（発音: "jack"）は、Rust 製の `jq` 互換 CLI です。`jq` とほぼ同じフィルタ構文をそのまま使えつつ、コンパイル言語ならではの高速な実行と、分かりやすいエラー表示が特徴です。実務では大きめの JSON を繰り返し処理する場面や、`jq` フィルタのデバッグをしたい場面で向いています。

## When to Use

- `jq` と同じ書き方のまま、処理速度を上げたいとき
- 大きな JSON ファイルやストリームを何度も加工・集計したいとき
- フィルタの構文ミスを、どこが間違っているか分かりやすく確認したいとき
- `jq` の一部の癖のある挙動（バグ）を避けたいとき

## Basic Syntax

```bash
jaq [options] '<filter>' [file]
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

## Related Links

- Download: https://github.com/01mf02/jaq/releases
- Install: https://github.com/01mf02/jaq#installation
- License: https://github.com/01mf02/jaq/blob/main/LICENSE-MIT
