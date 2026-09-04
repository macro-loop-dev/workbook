# for-william

Reviewer's notes. Not secret (the repo is public), but `.cursorignore` keeps
Cursor from quoting them at him, and AGENTS.md rule 6 tells the AI to stay out.
Deleted in the last commit of chapter 11: it is his repo by then.

## What "done" means, per rung

| Ch | Done when |
|---|---|
| 0 | Check block passes on his laptop; you marked ch 0 in front of him |
| 1 | He can say what `data-status` is; counter reads 2 of 12 |
| 2 | He rejected a diff and can say why; "mark chapter 2 done" produced a one-attribute diff |
| 3 | A local commit exists with a `docs:` prefix; the planted key was refused |
| 4 | A PR from a branch, gitleaks green, one review round survived |
| 5 | He merged; the `.pages.dev` counter changed; he pulled and deleted the branch |
| 6 | His domain serves the site; PR description carries the URL |
| 7 | Checkbox persists on his laptop and does not on his phone, and he can explain why |
| 8 | `npm test` green locally and in CI; a failing test you pushed, he fixed |
| 9 | Tick on the live site shows on his phone; nothing in git changed |
| 10 | Portfolio home, workbook under `/workbook/`, repo renamed, repo-doctor run together |
| 11 | FloatNote PR merged; `for-william/` deleted by his final PR |

## Running a checkpoint

30-60 minutes. Same shape every time:

1. He shows the Check section passing, unprompted.
2. He explains one thing in his own words (each chapter names it).
3. You do the one thing the chapter says you do (demo, review, dashboard click).
4. You do the out-of-sight thing if there is one (`dashboard-clicks.md`).
5. He writes his TIL line while you are still there.

Do not fix things for him at a checkpoint. Read the error out loud together
and ask what he thinks it says.

## Repo settings (GitHub)

Set before chapter 4, out of his sight, after his chapter-3 commit exists:

- Settings -> General -> Pull Requests: **allow merge commits only** (uncheck
  squash and rebase). Squash makes his local `main` diverge after every merge,
  and `git pull` then errors in chapter 5.
- Settings -> Branches -> add rule for `main`: require a pull request, require
  1 approval, do not allow bypassing. Relax to "require a PR, 0 approvals" at
  chapter 10.
- Settings -> Collaborators: him, with **write**.
- Actions: enabled (default). The only workflow is the gitleaks job.

## Manual verification that could not run in the scaffold session

The scaffold was built and checked on Linux. Two things need Windows or a
dashboard:

- Open the repo in Cursor; Settings -> Rules lists `workbook.mdc` as always
  applied; ask "mark chapter 1 done" and confirm the diff is one attribute.
- After connecting Cloudflare Pages (chapter 4 checkpoint), the deployment log
  says no build command, and `/chapters/01-edit-and-refresh.html` returns 200.

## Seeded Library

The eleven items in `library/index.html` were seeded by the scaffold on
2026-09-02 as placeholders in the right shape. Replace any you would not have
chosen; the `share-library` skill in dev-setup adds new ones as PRs.
