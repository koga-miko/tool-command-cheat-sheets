# coreutils Cheat Sheet

## Overview

Core Utilities は、Linux や Unix 系環境で長く使われている基本コマンド群です。実務ではファイル操作やログの簡易整形で頻繁に使います。Windows でも Microsoft.CoreUtils などの形で利用できるため、クロスプラットフォームな運用にも向いています。

## When to Use

- `ls`, `cp`, `mv`, `rm`, `mkdir` などの基本操作をしたいとき
- シェルスクリプト内で標準的なファイル操作をしたいとき
- ログの先頭・末尾を確認したいとき
- テキストを並び替えたり、重複を除去したいとき

## Basic Syntax

```bash
<command> [options] [arguments]
```

## Common Examples

```bash
# ディレクトリ一覧
ls -la

# ファイルをコピー
cp file.txt backup/file.txt

# ファイルを移動
mv old.txt new.txt

# ディレクトリを作成
mkdir -p build/output

# ファイルを削除
rm file.txt

# ディレクトリを中身ごと削除
rm -r build/

# ファイルの中身を表示
cat file.txt

# 空ファイルを作成 / タイムスタンプを更新
touch file.txt

# 文字列を表示
echo "hello"

# 先頭 10 行だけ表示
head -n 10 app.log

# 末尾 10 行だけ表示
tail -n 10 app.log

# 行を並び替え
sort names.txt

# 重複行を除去
uniq names.txt

# ディスク使用量を確認（ディレクトリ単位）
du -sh ./*

# 標準入力を受け取ってコマンドを実行
find . -name "*.log" | xargs rm

# 出力をファイルに保存しつつ画面にも表示
echo "done" | tee result.log

# 2つのファイルの差分を確認
diff old.txt new.txt
```

## Frequently Used Options

```bash
# 詳細表示
ls -la

# 再帰コピー
cp -r src/ dest/

# 確認なしで削除
rm -f file.txt

# 確認なしでディレクトリごと強制削除
rm -rf build/

# 既存ファイルを上書き
cp -f source target

# 連続処理用に表示
echo "hello" | cat

# 先頭 20 行を表示
head -n 20 app.log

# 末尾 20 行を表示
tail -n 20 app.log

# 並び替え後に重複を除去
sort names.txt | uniq

# 文字数やワード数も確認
wc -w app.log

# 行数だけ確認
wc -l app.log

# ログの追記をリアルタイムで追跡
tail -f app.log
```

## Notes

- Windows では `Microsoft.CoreUtils` を使うと扱いやすいです。
- `head` / `tail` / `sort` / `uniq` はログ確認や整形で便利です。
- PowerShell では挙動が少し違うので、使用するシェルを意識すると安心です。
- `uniq` は `sort` との組み合わせで使うことが多いです。
- `rm -rf` は取り消せないため、対象パスを必ず確認してから実行します。
- `xargs` はパイプで受け取った一覧に対して一括でコマンドを実行したいときに便利です。

## Related Links

- Download: https://github.com/microsoft/terminal/tree/main/src/TerminalCore/TerminalApp/Resources/Commandline/Commands/CoreUtils
- Install: https://learn.microsoft.com/windows/terminal/faq#how-do-i-install-coreutils-on-windows
- Docs: https://www.gnu.org/software/coreutils/manual/coreutils.html
- License: https://github.com/microsoft/terminal/blob/main/LICENSE
