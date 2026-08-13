# docker Cheat Sheet

## Overview

`docker` は、コンテナを作成・実行・管理するための CLI です。実務では開発環境の再現性や依存関係の隔離に向いています。

## When to Use

- アプリを隔離された環境で実行したいとき
- 依存関係の差異を避けたいとき
- ローカル開発環境を簡単に再現したいとき

## Basic Syntax

```bash
docker <command> [options]
```

## Common Examples

```bash
# イメージを取得
docker pull nginx

# コンテナを起動
docker run -d -p 8080:80 nginx

# 実行中コンテナを確認
docker ps

# ログを確認
docker logs <container>

# コンテナを停止
docker stop <container>

# コンテナ内でシェルを起動
docker exec -it <container> /bin/bash

# Dockerfile からイメージを作成
docker build -t myapp:latest .

# イメージ一覧を確認
docker images

# 複数コンテナを定義通りに起動（バックグラウンド）
docker compose up -d

# compose のログを確認
docker compose logs -f

# compose 環境を停止・削除
docker compose down

# ログをリアルタイムで追跡
docker logs -f <container>

# コンテナとホスト間でファイルをコピー
docker cp <container>:/app/log.txt ./log.txt
```

## Frequently Used Options

```bash
# バックグラウンド実行
docker run -d nginx

# ポートを公開
docker run -p 8080:80 nginx

# ボリュームをマウント
docker run -v $(pwd):/app nginx

# 環境変数を設定
docker run -e API_KEY=demo nginx

# ログを直近 100 行まで確認
docker logs --tail 100 <container>

# すべての停止中コンテナを削除
docker rm $(docker ps -aq)

# 未使用イメージを削除
docker image prune

# 停止中コンテナ・未使用ネットワーク・キャッシュなどをまとめて削除
docker system prune

# 使用していないボリュームも含めて削除
docker system prune --volumes

# 終了後に自動でコンテナを削除
docker run --rm -it nginx

# コンテナの詳細情報（IP・マウント等）を確認
docker inspect <container>

# CPU / メモリ使用状況をリアルタイム確認
docker stats

# イメージを削除
docker rmi myapp:latest

# ボリューム一覧・作成
docker volume ls
docker volume create mydata
```

## Notes

- `docker compose up -d` / `down` は複数コンテナ構成の開発環境で日常的に使います。
- 起動・停止・ログ確認は日常的な運用で重要です。
- 不要なリソースが溜まりやすいので `docker system prune` を定期的に使うとディスクを圧迫しにくいです。
- Windows では Docker Desktop の導入が前提になることがあります。

## Related Links

- Download: https://www.docker.com/get-started/
- Install: https://docs.docker.com/get-docker/
- Docs: https://docs.docker.com/reference/cli/docker/
- License: https://www.docker.com/legal/docker-software-end-user-license-agreement
