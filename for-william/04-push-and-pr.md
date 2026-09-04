# Chapter 4: reviewer notes

## Before the session

- Branch protection and merge-commits-only are on (`03-commit.md`, out of
  sight). Check: Settings -> Branches shows a rule on `main` requiring a PR
  and 1 approval; Settings -> General shows only "Allow merge commits" ticked.
- Your `share/<slug>` PR from the `share-library` skill is open, not merged,
  with him as reviewer, and its `secrets` check is green. Open it the day
  before so `gh pr list` shows it above his.
- Have `for-william/dashboard-clicks.md` open for the out-of-sight step.

## What to check

- A PR from `chapter-4` into `main`, title `chapter 4` (that is what
  `gh pr create --fill` makes from the branch name; fine), description edited
  to carry the three template headings with one line each.
- The PR holds at least six commits: his four from chapter 3, `docs: mark
  chapter 4 done`, `docs: til for chapter 4`. Files changed: `index.html`,
  `library/index.html`, `notes/index.html`, `notes/til.html`, two new notes.
- The `secrets` check is green on the latest commit.
- Your share PR is merged with a **merge commit**: the `main` commit list on
  GitHub shows `Merge pull request #N from macro-loop/share/...`. If it shows
  a single squashed commit instead, the repo setting was not saved; fix it now
  or chapter 5's `git pull` refuses.
- On his laptop: `git branch --show-current` is `chapter-4`;
  `git log --oneline --all --graph` shows `origin/main` on the merge commit
  and local `main` still on his chapter-3 commits. He did not pull. If he
  tried, `pull.ff only` refused with "Not possible to fast-forward"; no harm
  done, nothing to undo.
- Counter on his laptop: 5 of 12. On GitHub `main`: 0 of 12. He should be
  able to say why.

## What to ask

"Why are chapter 3's commits in this PR?" (never pushed; a PR is the branch
minus main.) "What ran when the check went green, and on which computer?"
(gitleaks, in `gates.yml`, on GitHub's runner; the hook's copy that cannot be
skipped.) "What did you check before approving mine?" (one line, right shape,
link works, `data-seen="no"`.) "Could you have merged your own PR?" (no:
review required, and not from the author.) "Why did I tell you not to pull?"
(his main and GitHub's main each have commits the other lacks; a pull that is
only allowed to fast-forward cannot line them up until his PR is merged.)

## What you do

- Review his PR for real. Under Files changed, leave exactly one comment on
  one line, phrased as a request with a reason. Good targets: the TIL
  sentence (ask for a concrete word instead of a vague one), or the note
  page's empty heading if he left one. Use Start a review -> Request changes
  so the yellow state is visible.
- Wait while he edits, commits, pushes. Refresh together: the new commit
  appears, the check re-runs, your comment shows as on an outdated version.
- Approve. Explain `.github/pull_request_template.md`: what changed is for
  the reader skimming, why is for the reader six months later, how I checked
  it is the sentence that stops "it looked done".
- Do **not** merge. Chapter 5 is the merge, and it needs the next step done
  first.

### Out of sight, at this checkpoint

After he leaves, connect Cloudflare Pages exactly as
`for-william/dashboard-clicks.md` says under "Chapter 4 checkpoint": project
Cloudflare account, Workers & Pages -> Create -> Pages -> Connect to Git,
authorize on `macro-loop` restricted to `workbook`, production branch
`main`, preset None, build command empty, output directory `/`, preview
deployments on all branches. The first deployment happens now from `main`,
which at this point is the scaffold plus your share item and all chapters
`todo`. Write the `*.pages.dev` URL into `05-merge-and-ship.md`. Confirm
`/chapters/01-edit-and-refresh.html` returns 200 (the README's manual check).
His merge in chapter 5 is then the first deploy he watches, and the counter on
the live site moving from 0 to 5 is the payoff.

## Common mistakes

- He committed chapter 4 on `main`, not on `chapter-4`. Symptoms: push says
  `Everything up-to-date` after he creates the branch, or his first push was
  to `main` and got `GH006`. Fix, run from his terminal on `main` with a clean
  tree:

  ```
  git switch -c chapter-4
  git branch -f main origin/main
  git push -u origin chapter-4
  ```

  Line 1 makes the branch at the same commit, so it carries all his commits.
  Line 2 moves the `main` label back to where GitHub has it (allowed because
  he is no longer on `main`). Line 3 as in the chapter. If he had already made
  a `chapter-4` branch before committing on `main`, delete the empty one first:
  `git branch -D chapter-4`.
- `gh pr create` without `--fill` asked questions and opened an editor.
  `Ctrl+C`, run it with `--fill`.
- He merged your share PR then ran `git pull` on `chapter-4` or `main`.
  `pull.ff only` refuses; nothing changed. Explain, move on.
- His PR shows an "Update branch" button after your share PR merged. Leave
  it; the merge in chapter 5 handles it. Clicking it makes a merge commit on
  his branch, which is harmless but confusing in the log.
- Credential Manager popup at push: browser sign-in once. If it loops, `gh auth
  setup-git` re-registers gh as the credential helper.
- Branch named `chpater-4` and pushed: `git branch -m chapter-4; git push -u origin chapter-4; git push origin --delete chpater-4`. Do it with him.
- Red `secrets` check: open the run log; a `Finding:` block names the file
  and commit. Almost certainly `scratch.txt` survived chapter 3. Fix is
  `git rm scratch.txt; git commit -m "chore: remove scratch file"; git push`,
  and the commit with the string stays in history on a branch that is never
  merged as-is: if that happens, close the PR, and rebuild the branch with him
  from `main` (`git switch main; git branch -D chapter-4; git switch -c chapter-4`,
  redo the two edits). Do not teach history rewriting today.
- Your share item and his seen item on adjacent lines: no effect today; the
  conflict, if any, shows at the chapter-5 merge. `03-commit.md` explains.

## Answer key

Commands, in order:

```
git status; git log --oneline -1
git switch -c chapter-4; git log --oneline -1
(edit the attribute)
git diff; git add index.html; git commit -m "docs: mark chapter 4 done"
git push -u origin chapter-4
gh pr create --fill
gh pr view --web
(edit the description on GitHub: three headings above the commit list)
gh pr checks --watch
gh pr list
gh pr diff <share PR number>
gh pr view <share PR number> --web   (approve, merge with a merge commit)
git fetch origin; git log --oneline --all --graph
(TIL line)
git add notes/til.html; git commit -m "docs: til for chapter 4"; git push
(your review comment -> his fix -> commit -> git push)
```

The mechanic, exactly one attribute:

```
-    <li data-chapter="04" data-status="todo"><span class="num">4</span><a href="chapters/04-push-and-pr.html">Push and open a pull request</a><span class="meta"><span class="badge">push + PR</span><span>2 h</span></span></li>
+    <li data-chapter="04" data-status="done"><span class="num">4</span><a href="chapters/04-push-and-pr.html">Push and open a pull request</a><span class="meta"><span class="badge">push + PR</span><span>2 h</span></span></li>
```

What `gh pr create --fill` produces with several commits (gh 2.x): title is
the branch name with `-` turned into a space, `chapter 4`; body is the commit
titles oldest first, each as a bold bullet:

```
- **docs: mark chapters 0 to 2 done and say who this is for**
- **docs: first notes and one library item seen**
- **docs: mark chapter 3 done**
- **docs: til for chapter 3**
- **docs: mark chapter 4 done**
```

The description after his edit, above that list:

```
## What changed
Chapters 0 to 4 marked done, my first two notes, one library item seen.

## Why
That is the work from chapters 1 to 4; chapter 3's commits were never pushed.

## How I checked it
Refreshed index.html: counter says 5 of 12. gitleaks check is green.
```

The share PR diff he approves is one added line, at the end of one category
list in `library/index.html`:

```
+    <li data-added="2026-MM-DD" data-seen="no"><a href="URL">Title</a> <span class="why">why</span></li>
```

Terminal equivalents of the web review: `gh pr review <n> --approve;
gh pr merge <n> --merge`.

Shape of `git log --oneline --all --graph` after the fetch (ids differ):

```
*   1f2e3d4 (origin/main, origin/HEAD) Merge pull request #6 from macro-loop/share/<slug>
|\
| * a9b8c7d feat(library): add <Title>
|/
| * c4d5e6f (HEAD -> chapter-4, origin/chapter-4) docs: til for chapter 4
| * 0a1b2c3 docs: mark chapter 4 done
| * b7e2f10 (main) docs: til for chapter 3
| * 9d41c2a docs: mark chapter 3 done
| * 5e0f7b3 docs: first notes and one library item seen
| * 3c9a1d4 docs: mark chapters 0 to 2 done and say who this is for
|/
* 70a06b5 chore: scaffold
```
