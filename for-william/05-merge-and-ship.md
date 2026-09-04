# Chapter 5: reviewer notes

Production URL: `https://________.pages.dev` (write it here at the chapter 4
checkpoint, when you connect Pages. He reads it off the dashboard; you read it
off this line.)

## Before the session

- His chapter-4 PR is approved and *not* merged. Do not merge it for him.
- Repo settings still say merge commits only (squash and rebase unchecked).
  If squash slipped back on, his `git pull` in step 7 fails; see Common
  mistakes for the recovery.
- Settings -> Builds -> preview deployments: all branches, so his chapter-5
  push gets a preview URL and the Cloudflare bot comments on the PR.
- He is a member of the project's Cloudflare account and can open the
  Deployments list (`dashboard-clicks.md`).

## What to check

- The live counter reads 5 of 12 after the chapter-4 merge, 6 of 12 after
  chapter 5's, on his laptop *and* his phone.
- `git branch` shows only `main`; `git status` says up to date with
  `origin/main`; `git log --oneline -3` starts with a `Merge pull request`
  commit. On GitHub the `chapter-4` and `chapter-5` branches are deleted.
- He clicked Merge himself. The PR's timeline shows his name on the merge.
- The chapter-5 PR diff is one attribute.

## What to ask

"What happens between clicking Merge and your phone showing 6?" Good answers
have four parts: `main` moved; GitHub told Cloudflare; Cloudflare fetched the
repo and copied the files to its servers; the phone asked the nearest copy.
"It uploads" is not enough; ask what uploaded what, to where.

"Which address would you give a friend, and why not the other one?"
(production; the preview has the branch name in front and shows a branch,
not `main`.)

"What did `Fast-forward` mean?" (his `main` moved ahead in a straight line;
nothing on his side that GitHub did not have.)

## What you do

- Rollback demo, in front of him: Workers & Pages -> workbook -> Deployments
  -> the previous production deployment -> Rollback to this deployment.
  Refresh the live site: 5 of 12, and `git log` is unchanged. Then put the
  newest back (same menu on the newest row). Say the sentence: every
  deployment is kept, going back is a click, shipping is not scary.
- Review his chapter-5 PR: one attribute, approve. Say nothing else; from
  chapter 7 you review for real.
- Out of sight: nothing new. Branch protection and merge-commits-only stay
  as set before chapter 4.

## Common mistakes

- **`git pull` fails: "Not possible to fast-forward" (his `pull.ff only`
  wording) or "You have divergent branches".** Cause: squash merge on the
  repo, or someone rewrote history. Fix, on his laptop, after confirming the
  PR is merged and `git status` shows nothing uncommitted:
  `git switch main; git fetch origin; git log origin/main..main --oneline`.
  Every commit listed must already be in the merged PR (same messages). Then
  `git reset --hard origin/main`. Explain it out loud: the same changes exist
  on `origin/main` under different commit ids, so his local copies are
  redundant. Then fix the repo setting so it does not recur.
- **Looking at the preview URL** and wondering why the counter did not move.
  Point at the branch name in the address bar.
- **Old content after Success.** Ctrl+F5, then wait a minute (CDN). Do not let
  him "fix" anything in the repo for this.
- **Deployment fails with "output directory dist not found".** The chapter 8
  build settings were applied early. Settings -> Builds -> output `/`, no build
  command, until chapter 8.
- **`git branch -d` refuses.** He deleted before pulling. Pull, then delete.
  Watch for `-D` suggested by the AI; ask him what the capital means before
  he types it.
- **Merge button grey.** Approval not on the latest commit (he pushed again
  after your approve), or the gitleaks check is still yellow.

## Answer key

The one-attribute diff for this chapter:

```
-    <li data-chapter="05" data-status="todo">
+    <li data-chapter="05" data-status="done">
```

Commit message: `docs: mark chapter 5 done`. Branch: `chapter-5`. PR opened
with `gh pr create --fill`, so the title is the commit message.

Shape of `git log --oneline --graph -6` on his `main` after the chapter-4
merge and pull (ids and PR number will differ):

```
*   a1b2c3d Merge pull request #3 from macro-loop/chapter-4
|\
| * 9f8e7d6 docs: mark chapter 4 done
| * 5c4b3a2 docs: mark chapter 3 done
| * 1a2b3c4 docs: first notes and one library item seen
|/
*   d4e5f6a Merge pull request #2 from macro-loop/share/...
```

The chapter-3 commits appear inside the chapter-4 PR because they were
never pushed on their own; chapter 4 explains that, and he should be able to
say it back.
