# curl Cheat Sheet

## Overview

`curl` は、HTTP/HTTPS リクエストを送るための CLI ツールです。実務では API 連携や疎通確認、ファイル取得でよく使います。

## When to Use

- API の動作確認をしたいとき
- サーバーに HTTP リクエストを送信したいとき
- ファイルをダウンロードしたいとき

## Basic Syntax

```bash
curl [options] <url>
```

## Common Examples

```bash
# GET リクエスト
curl https://example.com

# ヘッダー付きで取得
curl -i https://example.com

# JSON を整形して表示
curl -s https://api.example.com/data | jq

# ファイルを保存
curl -O https://example.com/file.tar.gz

# POST で送信
curl -X POST -H "Content-Type: application/json" -d '{"name":"demo"}' https://example.com/api

# リダイレクト先まで取得
curl -L https://example.com

# 認証付きで呼び出す
curl -u user:pass https://example.com/api
```

## Frequently Used Options

```bash
# レスポンスヘッダーも表示
curl -i https://example.com

# 成功時のみ表示
curl -f https://example.com

# リクエストヘッダーを付ける
curl -H "Authorization: Bearer <token>" https://example.com

# タイムアウトを指定
curl --max-time 10 https://example.com

# 何度も試行
curl --retry 3 https://example.com

# リトライ間隔を空けて再試行
curl --retry 3 --retry-delay 2 https://example.com

# ステータスコードだけ取得
curl -s -o /dev/null -w '%{http_code}\n' https://example.com
```

## Notes

- `-s` と `-f` を使うとスクリプト向けです。
- `-H` と `-d` を組み合わせると API テストに便利です。
- Windows / Linux / macOS でほぼ共通です。

## Related Links

- Download: https://curl.se/download.html
- Install: https://curl.se/docs/install.html
- License: https://curl.se/docs/copyright.html
