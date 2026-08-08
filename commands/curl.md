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

# JSON を送信（Content-Type / Accept を自動付与）
curl --json '{"name":"demo"}' https://example.com/api

# ファイルの中身を送信
curl -X POST -H "Content-Type: application/json" -d @body.json https://example.com/api

# ファイルをアップロード（multipart/form-data）
curl -F "file=@photo.png" https://example.com/upload

# PUT / DELETE で送信
curl -X PUT -d '{"name":"updated"}' https://example.com/api/1
curl -X DELETE https://example.com/api/1

# ヘッダーだけ取得（HEAD リクエスト）
curl -I https://example.com
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

# 通信の詳細（リクエスト/レスポンス）を確認
curl -v https://example.com

# GET のクエリパラメータを安全にエンコードして付与
curl -G --data-urlencode "q=hello world" https://example.com/search

# クッキーを保存 / 送信
curl -c cookies.txt https://example.com/login
curl -b cookies.txt https://example.com/dashboard

# レスポンスタイムを計測
curl -s -o /dev/null -w 'total: %{time_total}s\n' https://example.com

# 証明書検証をスキップ（自己署名証明書の検証環境など）
curl -k https://example.com
```

## Notes

- `-s` で静かな出力、`-f` で失敗時に終了コードを返すようにするとスクリプト向けです。
- `-H` と `-d` を組み合わせると API テストに便利です。
- `--json` は curl 7.82 以降で使える省略記法で、`Content-Type` と `Accept` を自動で付与します。
- `-k` は動作確認用途に限定し、本番相当の通信では証明書検証を無効化しないよう注意してください。
- Windows / Linux / macOS でほぼ共通です。

## Related Links

- Download: https://curl.se/download.html
- Install: https://curl.se/docs/install.html
- License: https://curl.se/docs/copyright.html
