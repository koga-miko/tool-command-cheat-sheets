# frawk Cheat Sheet

## Overview

`frawk` は、awk ライクな記法でテキストを処理するためのツールです。実務では CSV や TSV の簡易整形に向いています。

## When to Use

- CSV や TSV の整形・抽出をしたいとき
- 1行ごとのテキスト処理をしたいとき
- awk に近い記法で簡潔に処理したいとき

## Basic Syntax

```bash
frawk '<program>' [file...]
```

## Common Examples

```bash
# 1行ごとに出力
frawk '{print $0}' data.txt

# 2列目だけ表示
frawk '{print $2}' data.tsv

# 条件に一致する行だけ出力
frawk '$1 == "foo" {print $0}' data.tsv

# CSV から名前列だけ抽出
frawk -F',' '{print $2}' data.csv

# 先頭行をスキップして処理
frawk 'NR > 1 {print $1, $2}' data.csv
```

## Frequently Used Options

```bash
# 区切り文字を指定
frawk -F',' '{print $1}' data.csv

# 入力を分割
frawk -F'\t' '{print $1, $2}' data.tsv

# 実行時にヘッダを無視
frawk 'NR > 1 {print $0}' data.csv

# 1行目だけ処理
frawk 'NR == 1 {print $0}' data.csv
```

## Notes

- awk に近い書き方なので、既存スクリプトと相性が良いです。
- CSV や TSV の軽い変換に向いています。
- 複雑な処理は `jq` や `python` に寄せることもあります。

## Related Links

- Download: https://github.com/ericchiang/frawk/releases
- Install: https://github.com/ericchiang/frawk#installation
- License: https://github.com/ericchiang/frawk/blob/master/LICENSE
