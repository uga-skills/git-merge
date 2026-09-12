---
description: 指定したブランチを現在のブランチ（または指定した別ブランチ）に merge する。競合時は git-resolve-conflicts に委譲する
---

# Skill: git-merge

## 引数

- `git-merge <source>` → `<source>` を**現在のブランチ**に merge する。
- `git-merge <source> into <target>` → まず `<target>` に `switch` し、そのうえで `<source>` を merge する。

`<source>` の解釈は git-rebase と同様:

- `main` のようなローカルブランチ名 → そのままローカルの `<source>` を使う。
- `origin/main` のようなリモート追跡ブランチ名 → fetch 済みである前提でそのまま使う。**このスキル自身は `git fetch` を実行しない**。古い可能性がある場合は一言警告する。

## 前提条件（実行前に必ず確認）

- `git status` で作業ツリーがクリーンであることを確認する。未コミット/未ステージの変更がある場合は merge を開始せず、commit か stash をユーザーに促す。
- `into <target>` 指定がある場合、`switch` 前にも同様にクリーンであることを確認する（switch 自体が変更を破棄しうるため）。

## 手順

1. （`into <target>` 指定があれば）`git switch <target>` を実行する。
2. `<source>` が `origin/...` のようなリモート追跡ブランチ名の場合、fetch はせず、ローカルの当該ref が古い可能性がある旨を実行前に一言警告する。
3. `git merge <source>` を実行する。
4. 結果を判定する。
   - Fast-forward もしくは自動 merge 成功: `git log --oneline -5` で確認して報告し、終了。
   - コンフリクト発生: git-resolve-conflicts を実行し、解決後に merge commit を作成するところまで完了させる。
5. 完了後、`git status --short` で最終状態を確認する。

## 禁止事項

- `git merge --abort` をユーザーの明示的指示なく実行すること。
- merge commit のメッセージを勝手に書き換えること（デフォルトメッセージを尊重する。ユーザーが明示的に指定した場合のみ変更）。

## 出力

- 実行前に「何をどのブランチに merge するか」（switch の有無含む）を一言明示する。
- 完了後、コミットグラフの要約と、push が必要な場合は force push が不要な通常 push で足りる旨（merge は通常 rebase と異なり force push 不要）を報告する。
