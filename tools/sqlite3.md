# sqlite3 Cheat Sheet

## Overview

`sqlite3` は、ファイル1つで完結する軽量な RDB「SQLite」を操作するための公式 CLI です。実務では、ちょっとしたデータの永続化・検証用データベース・ローカルでの集計や動作確認に向いています。

## When to Use

- サーバーなしで手軽に DB を触りたいとき（ファイル1つで完結）
- CSV などのデータを取り込んで SQL で集計・加工したいとき
- アプリに組み込む前に、テーブル設計やクエリを手元で試したいとき

## Basic Syntax

```bash
sqlite3 [options] [database-file] ["SQL statement"]
```

## Common Examples

```bash
# データベースを開く（無ければ新規作成、REPL に入る）
sqlite3 app.db

# ワンライナーで SQL を実行して終了
sqlite3 app.db "SELECT * FROM users;"

# SQL ファイルをまとめて実行
sqlite3 app.db < schema.sql

# テーブル一覧を確認（REPL 内、または非対話）
sqlite3 app.db ".tables"

# テーブルの定義（スキーマ）を確認
sqlite3 app.db ".schema users"
```

```sql
-- テーブルを作成
CREATE TABLE users (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT NOT NULL,
  email TEXT UNIQUE,
  created_at TEXT DEFAULT CURRENT_TIMESTAMP
);

-- テーブルを削除
DROP TABLE users;

-- 列を追加（既存テーブルの変更）
ALTER TABLE users ADD COLUMN age INTEGER;

-- Create: 1件挿入
INSERT INTO users (name, email) VALUES ('demo', 'demo@example.com');

-- Create: 複数件まとめて挿入
INSERT INTO users (name, email) VALUES
  ('foo', 'foo@example.com'),
  ('bar', 'bar@example.com');

-- Read: 全件取得
SELECT * FROM users;

-- Read: 条件・並び替え・件数制限
SELECT id, name FROM users WHERE age >= 18 ORDER BY created_at DESC LIMIT 10;

-- Update: 条件に一致する行を更新
UPDATE users SET age = 20 WHERE id = 1;

-- Delete: 条件に一致する行を削除
DELETE FROM users WHERE id = 1;
```

## Frequently Used Options

```bash
# 出力にヘッダーを付ける / 列揃えして表示
sqlite3 app.db -header -column "SELECT * FROM users;"

# CSV として出力
sqlite3 app.db -csv "SELECT * FROM users;"

# CSV ファイルをテーブルに取り込む（1行目をヘッダーとして使う）
sqlite3 app.db ".import --csv --skip 1 data.csv users"

# 出力先をファイルに変更
sqlite3 app.db ".output result.csv" ".mode csv" "SELECT * FROM users;"

# データベース全体をバックアップ
sqlite3 app.db ".backup backup.db"

# データベースを SQL 文としてダンプ（再構築用）
sqlite3 app.db ".dump" > dump.sql

# 外部キー制約を有効化（SQLite はデフォルト無効）
sqlite3 app.db "PRAGMA foreign_keys = ON;"

# インデックスを作成
sqlite3 app.db "CREATE INDEX idx_users_email ON users(email);"

# クエリ結果を JSON（オブジェクトの配列）で出力
sqlite3 app.db -json "SELECT * FROM users;"

# REPL 内で JSON モードに切り替える場合
sqlite3 app.db ".mode json" "SELECT * FROM users;"

# jaq（や jq）にそのままパイプして解析する
sqlite3 app.db -json "SELECT * FROM users;" | jaq '.[] | select(.age >= 18)'
```

```sql
-- トランザクションでまとめて実行（大量 INSERT の高速化にも有効）
BEGIN;
INSERT INTO users (name) VALUES ('a');
INSERT INTO users (name) VALUES ('b');
COMMIT;

-- 失敗時は変更を取り消す
ROLLBACK;
```

## Notes

- SQLite は動的型付け（列に型を指定しても、実際にはどんな型の値でも入る）なので、意図しない型混入に注意します。
- `INSERT` を1件ずつ実行すると遅くなりがちです。大量投入は `BEGIN`〜`COMMIT` で1トランザクションにまとめると大幅に速くなります。
- 外部キー制約 (`FOREIGN KEY`) は既定で無効なので、使う場合は `PRAGMA foreign_keys = ON;` を毎接続時に実行する必要があります。
- `DROP TABLE` や `DELETE`（`WHERE` なし）は元に戻せないため、実行前に `.backup` や `.dump` でバックアップを取ると安心です。
- REPL 内のドットコマンド（`.tables` / `.schema` / `.mode` など）は SQL 文ではなく sqlite3 CLI 独自のコマンドで、末尾にセミコロンは不要です。
- `-json` / `.mode json` は各行がキーと値のオブジェクトになった配列を出力するので、[jaq](jaq.md) や [jq](jq.md) にそのままパイプして絞り込み・集計ができます。CSV と違い型（数値・null など）もある程度保持されます。

## Related Links

- Download: https://www.sqlite.org/download.html
- Docs (CLI): https://www.sqlite.org/cli.html
- License: https://www.sqlite.org/copyright.html
