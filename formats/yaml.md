# YAML Cheat Sheet

## Overview

YAML（YAML Ain't Markup Language）は、インデントで構造を表す人間に読みやすいデータ記述フォーマットです。実務では GitHub Actions、Docker Compose、Kubernetes マニフェストなど、設定ファイルとして広く使われます。

## When to Use

- CI/CD の設定ファイル（GitHub Actions, GitLab CI など）を書きたいとき
- Docker Compose や Kubernetes のマニフェストを書きたいとき
- コメント付きで人間が読み書きしやすい設定ファイルを用意したいとき

## Basic Syntax

```yaml
key: value
number: 1
flag: true
empty: null
list:
  - item1
  - item2
nested:
  key: value
```

## Common Examples

```yaml
# マッピング（オブジェクト相当）
name: demo
age: 20

# シーケンス（配列相当）
fruits:
  - apple
  - banana
  - cherry

# ネストした構造
user:
  name: demo
  roles:
    - admin
    - editor

# 複数行の文字列（改行を保持）
description: |
  1行目
  2行目

# 複数行の文字列（改行をスペースに変換）
summary: >
  長い文章を
  折り返して書く

# 1ファイルに複数ドキュメント
---
doc: 1
---
doc: 2
```

## Frequently Used Options

```yaml
# コメント
key: value # ここがコメント

# 文字列を明示的にクォート（数値や真偽値に解釈されるのを防ぐ）
version: "1.0"
zip_code: "0123"

# null の書き方（すべて同じ意味）
a: null
b: ~
c:

# アンカーとエイリアス（重複定義を避ける）
defaults: &defaults
  retries: 3
  timeout: 30
service:
  <<: *defaults
  name: web
```

## Notes

- インデントはスペースのみで、タブは使えません。
- `yes` / `no` / `on` / `off` / `true` / `false` は YAML 1.1 では真偽値と解釈されることがあります（通称 "Norway problem"、`NO` がノルウェーの国コードなのに `false` になってしまう例）。意図せぬ型変換を避けたい値は文字列としてクォートします。
- ツールによって準拠する YAML のバージョン（1.1 / 1.2）が異なるため、挙動に差が出ることがあります（例: Docker Compose は 1.1 系の解釈をすることがある）。
- コメントが書ける・読みやすいという利点がある一方、インデント崩れによる構文エラーが起きやすい点には注意が必要です。
- `yq`（`jq` の YAML 版）を使うと、CLI から YAML の整形・抽出・JSON への変換ができます。

## Related Links

- Spec: https://yaml.org/spec/1.2.2/
- Online Validator: https://www.yamllint.com/
- Learn YAML in Y minutes: https://learnxinyminutes.com/docs/yaml/
