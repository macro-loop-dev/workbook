# Chapter 10: reviewer notes

## What to check

- Four commits on the PR, one idea each: the move, the test fix, the redirect,
  the home page. If it is one commit, ask him to explain the diff anyway; do
  not block on it.
- `git log --follow workbook/chapters/01-edit-and-refresh.html` shows history
  back to the scaffold: he used `git mv`, not Explorer.
- No `../styles.css` left anywhere: `git grep -n '\.\./styles\.css'` is empty.
- `public/_redirects` is one line; `dist/_redirects` exists after a build.
- `src/chapters.test.ts` reads `workbook/chapters` and resolves root-relative
  links against the repo root (answer key below).
- On the live site after merge: `/`, `/workbook/`, one chapter, and an old
  `/chapters/...` URL returning a 301 to the new path
  (`curl -sI https://<domain>/chapters/01-edit-and-refresh.html` shows
  `location:`).
- A tick on `/workbook/` survives a phone refresh: the API path did not move.

## What to ask

"What would have broken if you had dragged the folder in Explorer instead of
`git mv`?" (Nothing on the site; the history of every page would read as
delete-and-create, and `git log --follow` and blame would lose the trail.)

"Why is the test failure in step 7 good news?"

## What you do

- Review the PR; the move commit is large but mechanical, so read it as
  "did anything change that is not a path".
- After the merge: Settings -> Branches -> the `main` rule: keep "require a
  pull request", set required approvals to 0. He ships alone from here.
- Rename follows on GitHub; check the Cloudflare Pages project still shows
  the git connection (Settings -> Builds) after the rename. Custom domain is
  unaffected.
- Run repo-doctor with him:
  `node ~\.claude\skills\repo-doctor\scripts\audit.mjs ~\dev\workbook --tier=internal`.
  Expect it to flag the missing `DEPLOYMENT.md` if he skipped it in chapter 8,
  and possibly the env contract (`.dev.vars.example` should satisfy it). Read
  every finding aloud; he writes the backlog note.
- Transfer to his account: only if you both want it, and last. It breaks the
  Pages git connection (reconnect in Settings -> Builds -> source) and moves
  branch protection with the repo. Update `share-library`'s REPO line in
  dev-setup either way (the rename changes it).

## Common mistakes

- Missed `../styles.css` in one chapter: the page renders unstyled.
- The brand link in every nav left pointing at `/workbook/index.html`.
- `_redirects` placed at the repo root instead of `public/`.
- Forgot `git remote set-url` and confused himself when forwarding lagged.

## Answer key

`vite.config.ts`, the pages block:

```ts
const pages: Record<string, string> = {
  index: "index.html",
  workbook: "workbook/index.html",
  library: "library/index.html",
};
for (const f of readdirSync("workbook/chapters")) {
  if (f.endsWith(".html")) pages[f.slice(0, -5)] = `workbook/chapters/${f}`;
}
```

`src/chapters.test.ts`, the two changes:

```diff
-const dir = join(import.meta.dirname, "..", "chapters");
+const root = join(import.meta.dirname, "..");
+const dir = join(root, "workbook", "chapters");
...
-    ok(`${f} links to ${m[1]}`, existsSync(join(dir, m[1])));
+    const target = m[1].startsWith("/") ? join(root, m[1]) : join(dir, m[1]);
+    ok(`${f} links to ${m[1]}`, existsSync(target));
```

`public/_redirects`:

```
/chapters/* /workbook/chapters/:splat 301
```

`src/workbook/main.ts`: imports gain one `..` (`../progress`, `../chapters`,
`../library`); the chapter link prefix becomes `/workbook/chapters/`. The
script tag in `workbook/index.html` and `library/index.html` is
`/src/workbook/main.ts`.

The home page is given in full in the chapter; the only required edits are
the name, the lede, and the GitHub link after the rename.
