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
# リポジトリを取得
git clone https://github.com/user/repo.git

# 状態確認
git status

# 変更をステージング
git add .

# コミット
git commit -m "feat: add new feature"

# 直前のコミットを修正
git commit --amend

# 直前のコミットメッセージをそのまま更新
git commit --amend --no-edit

# リモートに反映（初回は upstream を設定）
git push -u origin HEAD

# 2回目以降のリモート反映
git push origin HEAD

# 新しいブランチを作成
git switch -c feature/my-task

# 既存ブランチに切り替え
git switch main

# 変更を一時退避
git stash push -m "wip: before rebase"

# 退避した変更を復元
git stash pop

# 特定のファイルを取り戻す
git restore --source=HEAD -- path/to/file

# ステージングを取り消す（add を戻す）
git restore --staged path/to/file
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

# 特定コミットへの修正を作成し、後で自動的に取り込む
git commit --fixup=<commit-sha>
git rebase -i --autosquash upstream/main

# rebase・amend 後の安全な force push（他人の変更を上書きしない）
git push --force-with-lease

# ローカルブランチを削除
git branch -d feature/my-task

# リモートブランチを削除
git push origin --delete feature/my-task

# 消えたコミット・ブランチを履歴から探して復旧
git reflog

# 別ディレクトリに追加の作業ツリーを作成（並行作業向け）
git worktree add ../repo-feature feature/my-task

# 未追跡ファイルを確認してから削除
git clean -nd
git clean -fd

# タグを作成してプッシュ
git tag -a v1.0.0 -m "release v1.0.0"
git push origin v1.0.0
```

## Notes

- 実務では `status` / `diff` / `log` / `push` が基本です。
- ブランチ運用ルールがある環境では `switch` と `branch` を使い分けます。
- `git commit` ではメッセージを明確にするとレビューが楽になります。
- `amend` や `rebase` で履歴を書き換えた後の push は、共有ブランチを壊さないよう `--force`ではなく `--force-with-lease` を使うのが安全です。
- 細かい修正は `--fixup` + `rebase -i --autosquash` でまとめると、レビュー履歴がきれいになります。
- 誤って削除・reset したコミットは `reflog` から復旧できることが多いです。
- `git worktree` を使うと、ブランチ切り替えなしで複数の作業を並行して進められます（AI エージェントによる並行タスクとも相性が良いです）。

## Related Links

- Download: https://git-scm.com/downloads
- Install: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
- License: https://github.com/git/git/blob/master/COPYING
