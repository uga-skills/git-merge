---
name: git-merge
description: 指定したブランチを現在のブランチ（または指定した別ブランチ）に merge する。競合時は git-resolve-conflicts に委譲する。ユーザーが「mergeして」「このブランチに取り込んで」「mainを反映して」など、ブランチの merge を求めている場合は必ずこのスキルを使う。
---

# Skill: git-merge

## Arguments

- `git-merge <source>` → merge `<source>` into the **current branch**.
- `git-merge <source> into <target>` → first `switch` to `<target>`, then merge `<source>` into it.

`<source>` is interpreted the same way as in git-rebase:

- A local branch name like `main` → used as-is.
- A remote-tracking branch name like `origin/main` → assumed already fetched; used as-is. **This skill never runs `git fetch` itself.** Warn once if it may be stale.

## Preconditions (always check before running)

- Run `git status` and confirm the working tree is clean. If there are uncommitted/unstaged changes, do not start the merge — ask the user to commit or stash.
- If `into <target>` is given, check cleanliness again before switching (switching itself can discard changes).

## Steps

1. If `into <target>` was given, run `git switch <target>`.
2. If `<source>` is a remote-tracking branch like `origin/...`, do not fetch — warn once beforehand that the local ref may be stale.
3. Run `git merge <source>`.
4. Evaluate the result.
   - Fast-forward or automatic merge succeeds: confirm with `git log --oneline -5`, report, and finish.
   - Conflict: run git-resolve-conflicts, and see it through to creating the merge commit.
5. After completion, check the final state with `git status --short`.

## Rules (never violate)

- Running `git merge --abort` without the user's explicit instruction.
- Rewriting the merge commit message on your own (respect the default message; only change it if the user explicitly asks).

## Output

- Before merging, state in one line what is being merged into which branch (including whether a switch happens).
- After completion, summarize the commit graph, and note that a normal push (not force push) is sufficient if a push is needed (unlike rebase, merge doesn't require force push).
