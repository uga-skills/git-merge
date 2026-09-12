---
トークン使用量推定値: 768
計測方法: "Anthropic Messages API count_tokens (claude-sonnet-5)"
---

# git-merge

Claude Code用のmergeスキルです。
指定したブランチを現在のブランチ（または指定した別ブランチ）にmergeし、コンフリクトが起きた場合は[git-resolve-conflicts](https://github.com/uga-skills/git-resolve-conflicts)に解決を委譲します。

## Requires

- [git-resolve-conflicts](https://github.com/uga-skills/git-resolve-conflicts)（同じ場所に併せてinstallしてください。未installの場合、コンフリクト発生時に解決できません）

## Install

### すべてのプロジェクトで利用する場合

```bash
git clone git@github.com:uga-skills/git-merge.git ~/.claude/skills/git-merge
```

### 特定のプロジェクトでサブモジュールとして使う場合

```bash
git submodule add git@github.com:uga-skills/git-merge.git .claude/skills/git-merge
```

## 使い方

Claude Codeのチャットで以下のように実行します。

```
/git-merge main
```

現在のブランチに `main` をmergeします。別ブランチへswitchしてからmergeしたい場合は以下のように指定します。

```
/git-merge main into hoge
```

`hoge` にswitchしたうえで `main` をmergeします。

`origin/main` のようなリモート追跡ブランチ名を指定した場合、fetch済みである前提でそのまま使います（このスキル自身は`git fetch`を実行しません）。

実行前に作業ツリーがクリーンであることを確認し、汚れている場合はcommitかstashを促します。コンフリクトが発生した場合は[git-resolve-conflicts](https://github.com/uga-skills/git-resolve-conflicts)を呼び出して解決し、mergeコミットの作成までを一気通貫で行います。

## 注意

- `git merge --abort` はユーザーの明示的な指示なしには実行しません。
- mergeコミットのメッセージはデフォルトのまま尊重し、勝手に書き換えません。
