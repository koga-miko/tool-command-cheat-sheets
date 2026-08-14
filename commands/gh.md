# gh Cheat Sheet

## Overview

`gh`（GitHub CLI）は、GitHub の PR・Issue・リポジトリ操作をターミナルから行える公式 CLI です。実務では PR/Issue のやり取りや CI 状況確認に加えて、`gh api` を使うと GitHub の REST/GraphQL API を認証込みでそのまま叩けるため、Web UI や個別のトークン管理なしに GitHub 上のあらゆる情報を取得・操作できます。

## When to Use

- ターミナルから離れずに PR や Issue を作成・確認・マージしたいとき
- CI（GitHub Actions）の実行状況をさっと確認したいとき
- `gh api` で GitHub 上のデータ（Issue コメント、リリース、Organization 設定など）を直接取得・加工したいとき
- スクリプトや CI から GitHub に対して認証済みリクエストを送りたいとき

## Basic Syntax

```bash
gh <command> <subcommand> [flags]
gh api <endpoint> [flags]
```

## Common Examples

```bash
# 認証（ブラウザ経由 or トークン）
gh auth login

# 認証状態を確認
gh auth status

# リポジトリをクローン
gh repo clone owner/repo

# 現在のリポジトリをブラウザで開く
gh repo view --web

# 現在のブランチから PR を作成（タイトル・本文をコミットから自動生成）
gh pr create --fill

# PR 一覧を確認
gh pr list

# PR の詳細を確認
gh pr view 123

# PR をローカルにチェックアウト
gh pr checkout 123

# PR をマージ（squash マージ）
gh pr merge 123 --squash

# Issue を作成
gh issue create --title "bug: ログイン画面が真っ白" --body "手順: ..."

# Issue 一覧を確認
gh issue list
```

## gh api（REST / GraphQL への直接アクセス）

`gh api` は認証込みで GitHub の REST API v3 / GraphQL API v4 を直接叩けるサブコマンドです。エンドポイントに `{owner}` `{repo}` `{branch}` と書いておくと、カレントディレクトリのリポジトリの値に自動置換されます。

```bash
# 現在のリポジトリの情報を取得（{owner}/{repo} は自動置換される）
gh api repos/{owner}/{repo}

# Issue にコメントを投稿（-f で文字列パラメータを渡すと、自動的に POST になる）
gh api repos/{owner}/{repo}/issues/123/comments -f body='Hi from CLI'

# レスポンスを jq 構文でその場で絞り込む（--jq は gh に組み込み済み、別途 jq/jaq 不要）
gh api repos/{owner}/{repo}/issues --jq '.[].title'

# パラメータ付き GET（値を付けると既定で POST になるため、GET を明示する）
gh api -X GET search/issues -f q='repo:cli/cli is:open'

# ページネーションされた結果を全件取得
gh api repos/{owner}/{repo}/issues --paginate

# JSON ファイルをリクエストボディとして送信
gh api repos/{owner}/{repo}/rulesets --input file.json

# GraphQL API を叩く（-f query= にクエリ、変数は -F/-f で渡す）
gh api graphql -F owner='{owner}' -F name='{repo}' -f query='
  query($owner: String!, $name: String!) {
    repository(owner: $owner, name: $name) {
      releases(last: 3) { nodes { tagName } }
    }
  }
'
```

## Frequently Used Options

```bash
# -f/--raw-field: 常に文字列として送る
gh api repos/{owner}/{repo}/issues/123/comments -f body='comment text'

# -F/--field: 型推論あり（true/false/null/数値/@ファイル読込/{owner}などのプレースホルダを解釈）
gh api gists -F 'files[myfile.txt][content]=@myfile.txt'

# HTTP メソッドを指定（更新・削除など）
gh api -X PATCH repos/{owner}/{repo}/issues/123 -f state='closed'

# カスタムヘッダーを追加
gh api -H 'Accept: application/vnd.github.v3.raw+json' repos/{owner}/{repo}/contents/README.md

# レスポンスをキャッシュ（同じリクエストを繰り返す場合に便利）
gh api --cache 1h repos/{owner}/{repo}

# ステータス行・レスポンスヘッダーも表示（デバッグ用）
gh api -i repos/{owner}/{repo}

# 別リポジトリを指定して実行（-R はほとんどの gh コマンドで共通）
gh pr list -R owner/other-repo

# PR/Issue のコメントを直接取得する例
gh api repos/{owner}/{repo}/pulls/123/comments
```

## Notes

- `-f`（raw-field）はすべて文字列として送られ、`-F`（field）は `true`/`false`/`null`/整数、`@filename` によるファイル読込、`{owner}` などのプレースホルダを解釈します。用途に応じて使い分けます。
- パラメータ（`-f`/`-F`）を1つでも付けると、既定の HTTP メソッドが `GET` から `POST` に自動で変わります。GET のままクエリパラメータを付けたい場合は `-X GET` を明示します。
- `--jq` は gh に組み込まれた jq 相当のフィルタで、`jq`/[jaq](../tools/jaq.md) を別途インストールしなくてもレスポンスをその場で絞り込めます。複雑な加工をしたい場合は `gh api ... | jaq '...'` のように外部にパイプすることもできます。
- 認証トークンは `gh auth login` の対話フローの他に、`GH_TOKEN` / `GITHUB_TOKEN` 環境変数でも指定できます（CI 上での利用に便利）。
- `gh api` に限らず、`gh pr` / `gh issue` / `gh repo` など多くのコマンドは `-R owner/repo` で対象リポジトリを明示的に切り替えられます（省略時はカレントディレクトリのリポジトリ）。

## Related Links

- Download: https://cli.github.com/
- Install: https://github.com/cli/cli#installation
- Docs: https://cli.github.com/manual/
- License: https://github.com/cli/cli/blob/trunk/LICENSE
