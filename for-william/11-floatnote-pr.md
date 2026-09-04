# Chapter 11: reviewer notes

## Before

- Add him as a collaborator on `macro-loop/floatnote` with write.
- Confirm `main` is green on a clean checkout yourself, the same day. If his
  step 3 is red, it is yours, and the chapter tells him to stop and say so.
- Decide whether you want him on menu item 4 (settings toggle). Default: no,
  first time.

## What to check on the PR

- One slice: two files for menu item 1 (`src/docs/07-units-tables.txt`,
  `src/docs.test.ts`), nothing else. Any `lint:fix` collateral is the first
  comment.
- The docs line has no trailing full stop and is not tag-shaped.
- The `answers("...")` string matches the docs line exactly.
- Branch name has a prefix; commit message has a prefix; PR body has the three
  parts.
- CI green on the PR head.

Review as you would anyone: at least one real comment, even if it is a
wording nit, so he gets the change-push-reply loop once on a repo that is not
his.

## Menu with exact paths

1. Docs example: `src/docs/07-units-tables.txt` (length list, after
   `6 ft + 2 inches in cm`), guard in `src/docs.test.ts` next to the two
   existing `answers(...)` calls. The test uses `evaluateDoc` with fixed rates
   and a fixed date, so unit lines are deterministic. Worked example:

   ```
   100 km in mi
   ```
   ```ts
   answers("100 km in mi");
   ```

2. Landing copy: `landing/index.html`, self-contained, deployed by
   `npm run deploy:landing` (yours, not his).
3. Ranking case: `src/fuzzy.test.ts`, one more `eq("...", rank(...), [...])`
   in the ranking section; he must check the expected order in the running
   app first, not guess it.
4. Settings toggle: `src/settings.ts` (`AppSettings`, additive only) and
   `src/app-settings.ts` (copy the `lineNumbers` row). This is a `feat:` and
   needs a manual check per `TESTING.md`; only with you pairing.

## Traps

- Anything mentioning `npx tauri`, ARM64, or the MSVC target. Not his.
- `npm ci` fetches `xlsx` from a non-npm host; needs network once.
- `npm test` runtime; the `mcp-live` suite spawns a local fixture server.
- Biome formatting of untouched files.
- Committing on `main` and hitting branch protection; the chapter gives the
  recovery.

## After the merge

- His site: chapter 11 ticked via the API; the `chore/its-mine` PR deletes
  `for-william/` and empties `.cursorignore`. Approve it; that is the last
  required review.
- Update `share-library`'s REVIEWER/REPO lines in dev-setup if anything
  changed at chapter 10.
- The "what next" conversation: a project of his own, on his site, same
  ladder. Not the same day.
