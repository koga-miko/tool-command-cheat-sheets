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

# CSV として正しくパースして列を表示（クォート内のカンマも1列として扱える）
frawk -i csv '{print $1, $3}' data.csv

# CSV → TSV に変換
frawk -i csv -o tsv '{print}' data.csv

# TSV → CSV に変換
frawk -i tsv -o csv '{print}' data.tsv

# 入出力とも CSV として扱う省略形
frawk --csv '{print $1}' data.csv

# 列の合計を計算
frawk -i csv '{sum += $2} END {print sum}' data.csv
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

# 入力フォーマットを明示的に指定
frawk -i csv '...' data.csv
frawk -i tsv '...' data.tsv

# 出力フォーマットを明示的に指定
frawk -o csv '...' data.txt

# 入出力を同じフォーマットに揃える省略形
frawk --csv '...' data.csv
frawk --tsv '...' data.tsv

# 変数を渡す
frawk -v threshold=100 -i csv '$2 > threshold' data.csv

# 並列処理で高速化
frawk -i csv -j 4 '{sum += $2} END {print sum}' data.csv
```

## Notes

- awk に近い書き方なので、既存スクリプトと相性が良いです。
- `-F` は単純な区切り文字での分割なので、クォート内にカンマを含む CSV では崩れることがあります。正しくパースしたい場合は `-i csv` / `-i tsv` を使います。
- `-i` / `-o` はそれぞれ入力・出力フォーマットの指定で、`--csv` / `--tsv` は両方を同じフォーマットに揃える省略形です。CSV↔TSV 変換にもそのまま使えます。
- 複雑な処理は `jq` や `python` に寄せることもあります。
- Windows は作者自身も「テストしていない」としており、公式サポート外です。`cargo install frawk` をデフォルト設定のまま実行すると、既定で有効な `use_jemalloc`（Windows/MSVC でビルド不可）や `llvm_backend`（LLVM 12 の用意が必要）が原因でビルドが失敗します。

```bash
# Windows でビルドする場合は jemalloc / LLVM backend / nightly 機能をすべて無効化する
cargo install frawk --no-default-features
```

## Related Links

- Download: https://github.com/ezrosent/frawk/releases
- Install: https://github.com/ezrosent/frawk#installation
- Docs: https://github.com/ezrosent/frawk/blob/master/README.md
- License: https://github.com/ezrosent/frawk/blob/master/LICENSE-MIT
