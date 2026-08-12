# JSON Cheat Sheet

## Overview

JSON（JavaScript Object Notation）は、テキストベースの軽量なデータ交換フォーマットです。実務では API のリクエスト/レスポンス、設定ファイル、異なる言語・システム間でのデータのやり取りに広く使われます。

## When to Use

- API のリクエストボディやレスポンスを扱いたいとき
- 言語・システムをまたいだ設定ファイルを書きたいとき
- 構造化データをシンプルな形式で保存・受け渡ししたいとき

## Basic Syntax

```json
{
  "key": "value",
  "number": 1,
  "flag": true,
  "empty": null,
  "list": [1, 2, 3],
  "nested": { "key": "value" }
}
```

## Common Examples

```json
// オブジェクト（キーは必ずダブルクォート）
{ "name": "demo", "age": 20 }

// 配列
["apple", "banana", "cherry"]

// ネストした構造
{
  "user": {
    "name": "demo",
    "roles": ["admin", "editor"]
  }
}

// 真偽値・null
{ "active": true, "deletedAt": null }

// 数値（先頭ゼロ・末尾のカンマは不可）
{ "price": 19.99, "count": 3 }
```

## Frequently Used Options

```bash
# 整形して表示（jq を使う例）
cat data.json | jq .

# 1行に圧縮する
cat data.json | jq -c .

# 構文チェック（パースできるか確認）
cat data.json | jq empty && echo "valid"

# 特定のキーだけ抽出
cat data.json | jq '.user.name'
```

## Notes

- コメントは書けません（`//` や `/* */` は使用不可）。設定ファイルでコメントが必要な場合は JSONC や YAML を検討します。
- 末尾カンマ（trailing comma）はエラーになります。
- キーは必ずダブルクォートで囲みます（シングルクォートや無クォートは不可）。
- 数値の先頭ゼロ（`01` など）は無効です。
- 整形・抽出・検証には [jq](../tools/jq.md) や [jaq](../tools/jaq.md) が便利です。

## Related Links

- Spec: https://www.json.org/json-ja.html
- Spec (RFC): https://datatracker.ietf.org/doc/html/rfc8259
- MDN: https://developer.mozilla.org/ja/docs/Learn/JavaScript/Objects/JSON
