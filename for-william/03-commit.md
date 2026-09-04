# Chapter 3: reviewer notes

## What to check

- `git log --oneline` inside `~\dev\workbook` shows four commits of his above
  the `origin/main` marker, newest first:
  1. `docs: til for chapter 3`
  2. `docs: mark chapter 3 done`
  3. `docs: first notes and one library item seen`
  4. `docs: mark chapters 0 to 2 done and say who this is for`
  Wording may differ; the `docs:` prefix and one sentence each should not.
- `git log --stat` shows commit 4 touching only `index.html`, commit 3 touching
  `library/index.html`, `notes/index.html`, `notes/til.html` and two new
  `notes/2026-*.html`, commit 2 touching `index.html` with `1 +, 1 -`.
- `git config core.hooksPath` prints `.githooks`. Run it in the repo folder;
  it is a local setting.
- `Test-Path scratch.txt` is `False`. `git status` is clean.
- `git log -1 --format="%an <%ae>"` matches his GitHub name and email.
- Write down which Library item he marked seen (`git show --stat HEAD~1` and
  `git diff HEAD~2 HEAD~1 -- library/index.html`). You need it below.

## What to ask

"Read the four commits from the bottom up. What is in each?" Then: "What did
the hook see, and what did it print instead of the key?" (REDACTED; the shape,
not the value.) Then the river question: "Where do these four commits exist
right now?" Wait for "only here". Then: "Where should the second copy of the
gitleaks check live?" He does not know; a guess of "on GitHub" is the answer
chapter 4 gives.

## What you do

- Read the log together, `git log --stat`, then open `.githooks/pre-commit`
  and read the four lines out loud.
- Ask the river question and let the silence sit.
- Do not push anything for him. The point of the ending is the discomfort.

### Out of sight, after this checkpoint

Do these once his chapter-3 commits exist and before he starts chapter 4.
They are listed in `for-william/README.md` under Repo settings; the exact
clicks:

- GitHub -> `macro-loop-dev/workbook` -> Settings -> General -> Pull Requests:
  untick **Allow squash merging** and **Allow rebase merging**; leave **Allow
  merge commits** ticked. Squash would make his local `main` diverge after
  every merge and `git pull` would refuse in chapter 5.
- Settings -> Branches -> Add branch ruleset (or classic rule) for `main`:
  require a pull request before merging, required approvals 1, do not allow
  bypass. Relax to 0 approvals at chapter 10.
- Settings -> Collaborators: he has **write** (should already be true from
  chapter 0's clone; confirm).
- The day before his chapter-4 session, share one Library item with the
  `share-library` skill from dev-setup. It opens a PR on `share/<slug>`
  titled `Library: <Title>` with him as reviewer. Do not merge it; he does,
  in chapter 4. Pick a category whose last item is **not** the one he marked
  seen in chapter 1: the skill appends to the end of the `<ul>`, his commit
  changed `data-seen` on a line, and two edits on adjacent lines conflict
  when his branch merges in chapter 5. If the seen item is the last line of
  every category you want, accept the conflict and plan to resolve it with
  him on GitHub's conflict editor in chapter 5 (keep both lines).

## Common mistakes

- `git config core.hooksPath .githooks` run in the wrong folder, so the fake
  key commits silently. Undo with him: `git reset --soft HEAD~1; git restore --staged scratch.txt; Remove-Item scratch.txt`.
- `gitleaks: command not found` from the hook: PATH inside Cursor's terminal
  is stale. Close Cursor fully and reopen; `gitleaks version` in a fresh pwsh
  must work first.
- He typed his own "random" token. If it was a keyboard walk or a sequence
  like `abcdefghijklmnopqrstuvwxyz`, gitleaks will not fire: that string is in
  its global stopword list, and `AKIAIOSFODNN7EXAMPLE` (the AWS docs example)
  is allowlisted too. The token in the chapter was tested against gitleaks
  8.28 and trips rule `github-pat`; use that one.
- Vim opened because `-m` was forgotten: `Esc`, `:q!`, `Enter`.
- Wrong email on commits: `git config --global user.email`, then
  `git commit --amend --reset-author --no-edit` fixes only the last one. If
  all four are wrong, leave them; they are local and it is not worth a rebase
  lesson today. GitHub will still attribute pushes to his account.
- He used the Source Control panel. Not wrong, but he skipped `git add` and
  never saw the hook output. Have him do the mechanic commit again in the
  terminal if the hook lines were never seen.
- "LF will be replaced by CRLF": harmless, `.gitattributes` handles it.

## Answer key

First commit, `index.html` only (his paragraph wording will differ):

```
-    <li data-chapter="00" data-status="todo"><span class="num">0</span><a href="chapters/00-setup.html">Set up the machine</a><span class="meta"><span class="badge">watch</span><span>2-3 h</span></span></li>
-    <li data-chapter="01" data-status="todo"><span class="num">1</span><a href="chapters/01-edit-and-refresh.html">Edit the file, refresh the browser</a><span class="meta"><span class="badge">edit + refresh</span><span>1 h</span></span></li>
-    <li data-chapter="02" data-status="todo"><span class="num">2</span><a href="chapters/02-ask-the-ai.html">Ask the AI, review the diff</a><span class="meta"><span class="badge">ask the AI</span><span>1-2 h</span></span></li>
+    <li data-chapter="00" data-status="done"><span class="num">0</span><a href="chapters/00-setup.html">Set up the machine</a><span class="meta"><span class="badge">watch</span><span>2-3 h</span></span></li>
+    <li data-chapter="01" data-status="done"><span class="num">1</span><a href="chapters/01-edit-and-refresh.html">Edit the file, refresh the browser</a><span class="meta"><span class="badge">edit + refresh</span><span>1 h</span></span></li>
+    <li data-chapter="02" data-status="done"><span class="num">2</span><a href="chapters/02-ask-the-ai.html">Ask the AI, review the diff</a><span class="meta"><span class="badge">ask the AI</span><span>1-2 h</span></span></li>
```

plus the one added paragraph from chapter 2.

The mechanic commit, `docs: mark chapter 3 done`:

```
-    <li data-chapter="03" data-status="todo"><span class="num">3</span><a href="chapters/03-commit.html">Commit in the terminal</a><span class="meta"><span class="badge">commit</span><span>2 h</span></span></li>
+    <li data-chapter="03" data-status="done"><span class="num">3</span><a href="chapters/03-commit.html">Commit in the terminal</a><span class="meta"><span class="badge">commit</span><span>2 h</span></span></li>
```

The commands, in order:

```
git status
git diff index.html
git add index.html; git status
git commit -m "docs: mark chapters 0 to 2 done and say who this is for"
git add notes library; git commit -m "docs: first notes and one library item seen"
git log --oneline
git config core.hooksPath .githooks; git config core.hooksPath; gitleaks version
Set-Content -Path scratch.txt -Value ('github_token = "ghp_' + 'cI3qS7xOX7ZRdDOU7RcflxMNwhusuWJnVDQv' + '"')
git add scratch.txt; git commit -m "test: try to commit a fake key"
git restore --staged scratch.txt; Remove-Item scratch.txt; git status
(edit the attribute)
git diff
git add index.html; git commit -m "docs: mark chapter 3 done"
git add notes/til.html; git commit -m "docs: til for chapter 3"; git status
```

The planted file `scratch.txt` is one line: `github_token = "` + the two halves
joined + `"`. It is written split here for the same reason as in the chapter:
this file is in the repo and the hook scans it. The token is fabricated; it
was never issued.

Expected hook output on the planted commit (gitleaks 8.28, `--redact`):

```
Finding:     github_token = "REDACTED"
Secret:      REDACTED
RuleID:      github-pat
Entropy:     4.853056
File:        scratch.txt
Line:        1
Fingerprint: scratch.txt:github-pat:1

9:43PM INF 0 commits scanned.
9:43PM INF scanned ~58 bytes (58 bytes) in 240ms
9:43PM WRN leaks found: 1
```

and no `[main ...]` line after it; exit code 1, commit not created.

Expected `git log --oneline` at the end (ids differ):

```
b7e2f10 (HEAD -> main) docs: til for chapter 3
9d41c2a docs: mark chapter 3 done
5e0f7b3 docs: first notes and one library item seen
3c9a1d4 docs: mark chapters 0 to 2 done and say who this is for
70a06b5 (origin/main, origin/HEAD) chore: scaffold
```

If you pushed commits to `main` after the scaffold (replacing seeded Library
items, for instance), those sit between his and the scaffold and the
`origin/main` marker is on the newest of them.
