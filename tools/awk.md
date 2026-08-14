# awk Cheat Sheet

## Overview

`awk` は、行指向のテキスト処理を行うための古典的なプログラミング言語/コマンドです（[frawk](frawk.md) はこの awk を Rust で高速に再実装したもの）。実務ではログの集計、区切りテキストからの列抽出、簡易レポート生成に向いています。frawk が Windows でビルド・実行に手間取る場合の実用的な代替になります。

Windows では2つの入手経路があります。

- **Git Bash を使っている場合**: Git for Windows に GNU Awk（gawk）が `awk` として最初から同梱されており、追加インストール無しで使えます（本チートシートは Git Bash 上の `GNU Awk 5.0.0` で動作確認済み）。
- **素の cmd.exe / PowerShell で使いたい場合**: `Microsoft.CoreUtils`（[coreutils](../commands/coreutils.md)）には awk は含まれていません（ソースの `Cargo.toml` を確認済み。coreutils 本体 + findutils + grep のみで awk は無し）。代わりに Chocolatey で `choco install gawk` を使うと、cmd.exe だけでも動く単体の gawk（Windows 向け、Chocolatey 上で Trusted/Approved 済み）を入れられます。

## When to Use

- Windows でテキスト処理をしたいとき（Git Bash なら追加インストール不要、素の cmd.exe/PowerShell でも `choco install gawk` ですぐ使える）
- ログや区切りテキストから特定の列を抽出・集計したいとき
- ちょっとした集計やレポートをワンライナーで済ませたいとき

## Basic Syntax

```bash
awk 'pattern { action }' [file...]
awk -F',' '{ action }' [file...]
awk -f script.awk [file...]
```

## Common Examples

```bash
# 1行ごとに出力
awk '{print}' data.txt

# 2列目だけ表示（既定の区切りは空白・タブ）
awk '{print $2}' data.txt

# 区切り文字を指定して列を抽出
awk -F',' '{print $2}' data.csv

# 条件に一致する行だけ出力
awk '$1 == "foo" {print}' data.tsv

# 先頭行（ヘッダー）をスキップして処理
awk 'NR > 1 {print $1, $2}' data.csv

# 列の合計を計算
awk -F',' '{sum += $2} END {print sum}' data.csv

# 行数をカウント
awk 'END {print NR}' data.txt

# 列を書き換えて出力（区切り文字を保つ）
awk -F',' 'BEGIN{OFS=","} {$2 = $2 * 1.1; print}' data.csv
```

## Frequently Used Options

```bash
# 区切り文字を指定
awk -F',' '{...}' data.csv

# 変数を渡す
awk -v threshold=100 '$2 > threshold' data.csv

# BEGIN/END ブロック（処理の開始前・終了後に1回だけ実行）
awk 'BEGIN{print "start"} {print} END{print "end"}' data.txt

# 文字列を置換
awk '{gsub(/foo/, "bar"); print}' data.txt

# printf で整形出力
awk '{printf "%-10s %5d\n", $1, $2}' data.txt

# 連想配列で集計（列ごとの出現回数をカウント）
awk -F',' '{count[$1]++} END {for (k in count) print k, count[k]}' data.csv

# 複数ファイルをまとめて処理し、ファイル名も表示
awk '{print FILENAME, NR, $0}' a.txt b.txt

# スクリプトファイルから実行
awk -f script.awk data.txt
```

## Notes

- `NR`（これまでに処理した総行数）、`NF`（現在行のフィールド数）、`FILENAME`（処理中のファイル名）、`$0`（行全体）はよく使う組み込み変数です。
- フィールドを書き換えてから `print` すると `OFS`（既定は半角スペース）で再結合されます。区切り文字（例: カンマ）を保ったまま列を書き換えたい場合は、`OFS` も入力と同じ文字に設定してください。
- 素の `awk` の `-F` は単純な区切り文字での分割です。[frawk](frawk.md) の `-i csv` のようにクォート内のカンマや改行を正しく扱う機能は無いため、複雑な CSV（フィールド内にカンマや改行を含む）には不向きです。そうした CSV を厳密に扱いたい場合は [jaq](jaq.md)（`--from csv`）などを検討してください。
- 実装によって挙動に差があります（GNU awk = gawk、オリジナルの One True Awk、mawk など）。Windows で Git Bash 経由で `awk` と打つ場合は基本的に gawk です。
- 素の cmd.exe / PowerShell だけで gawk を使いたい場合は、`choco install gawk`（Chocolatey、Trusted/Approved パッケージ、確認時点で v5.3.2）、`scoop install gawk`（Scoop の公式 main バケットに存在）、MSYS2（`pacman -S gawk`）のいずれかでインストールできます。`Microsoft.CoreUtils` を入れても awk は付いてこないため、別途これらのいずれかが必要です。
- 大きなデータを高速に処理したい場合や CSV/TSV を正しくパースしたい場合は、同じ書き方の延長で使える [frawk](frawk.md)（Windows では制約あり）や [jaq](jaq.md) も選択肢になります。

## Related Links

- Docs (GNU Awk User's Guide): https://www.gnu.org/software/gawk/manual/gawk.html
- Download: https://www.gnu.org/software/gawk/
- Install (Git Bash に同梱): https://gitforwindows.org/
- Install (cmd.exe/PowerShell 向け, Chocolatey): https://community.chocolatey.org/packages/gawk
- License: https://www.gnu.org/licenses/gpl-3.0.html
