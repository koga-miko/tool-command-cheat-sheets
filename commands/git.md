# git Cheat Sheet

## Overview

`git` は、ソースコードの変更履歴を管理するための分散型バージョン管理システムです。実務ではブランチ運用やレビュー対応で欠かせないツールです。

## When to Use

- 変更内容を保存したいとき
- ブランチを切って作業したいとき
- 複数人作業で差分を確認したいとき

## Basic Syntax

```bash
git <command> [options]
```

## Common Examples

```bash
# 状態確認
git status

# 変更をステージング
git add .

# コミット
git commit -m "feat: add new feature"

# リモートに反映
git push origin HEAD

# 新しいブランチを作成
git switch -c feature/my-task

# 既存ブランチに切り替え
git switch main

# 直前のコミットを修正
git commit --amend

# 変更を一時退避
git stash push -m "wip: before rebase"

# 特定のファイルを取り戻す
git restore --source=HEAD -- path/to/file
```

## Frequently Used Options

```bash
# 変更内容を確認
git diff

# 直近の履歴を確認
git log --oneline --decorate --graph -n 10

# すべてのブランチと追跡状態を確認
git branch -vv

# リモート設定を確認
git remote -v

# 途中保存の内容を確認
git stash list

# 直前のコミットをまとめて修正
git rebase -i HEAD~3

# リモートの最新を取り込んで再適用
git pull --rebase

# upstream の main に rebase
git fetch upstream
git rebase upstream/main

# rebase を中断
# (コンフリクト発生時)
git rebase --abort

# コンフリクトを中断して戻す
git merge --abort

# 解決済みのファイルを add
git add path/to/file

# 指定コミットを別ブランチに適用
git cherry-pick <commit-sha>
```

## Notes

- `status` / `diff` / `log` / `push` は基本です。
- ブランチ運用ルールがある場合は `switch` を使うと分かりやすいです。
- コミットメッセージはレビューしやすい形にすると便利です。

## Related Links

- Download: https://git-scm.com/downloads
- Install: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
- License: https://github.com/git/git/blob/master/COPYING
