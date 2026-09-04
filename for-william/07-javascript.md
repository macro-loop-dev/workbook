# Chapter 7: reviewer notes

## Before the session

- His domain works and 7 of 12 is live (chapters 0 to 6 done in
  `index.html`). The chapter's numbers (7, 8, 58%, two-thirds) assume that.
- Cursor still on the free tier is fine; the AI writes two small functions.
- Confirm his browser is Edge or Chrome in a normal window. Private windows
  and some settings drop localStorage, and step 10's disk path is Edge's.

## What to check

- Four files in the PR: `script.js` (new), `styles.css`, `index.html`,
  `library/index.html`. Nothing else.
- `script.js` is a classic script: no `import`/`export`, no `fetch`, no
  `type="module"` on either tag. Both tags are the last thing inside
  `<body>`; the Library one says `../script.js`.
- The two storage names are exactly `workbook.progress.v1` and
  `workbook.seen.v1`, and the stored shape is `{"07":"done"}` (check in the
  Application tab).
- The script writes the merged state back to `li.dataset.status` /
  `li.dataset.seen`, so the CSS counter and the `new` badges keep working
  from the attributes. If he built a separate counter in JS instead, that is
  the review comment.
- Both pages guard: `index.html` gets no library code, `library/index.html`
  gets no bar, and a chapter page would run the script without an error.
- The bar width is set with `fill.style.width` from JS and nowhere else.
  No `style=` in any HTML, no hex in `styles.css` below the tokens.
- `styles.css` gains the five rules in the answer key and only tokens.
- Two commits on the branch: `feat:` then `docs:`. Live site after merge:
  checkboxes and 8 of 12 on his phone.

## What to ask

"Why did the phone and the laptop disagree, and which truth did the PR
change?" Good answers: the file (shared, deployed) and the browser's drawer
(local, per origin); the PR shipped the file's version; the phone's tick on
chapter 8 lived only on the phone. Then: "so where would a shared tick have
to live?" He should reach for "somewhere both can ask" without the word
server; chapter 9 gives him the word.

"Walk me through `countDone`." Every line, including why `===` and what
`dataset` is. Then: "what would it return if a chapter said
`data-status="finished"`?" (not counted; chapter 1's question again, now
from the JS side.)

"Why does the storage name start with `workbook.` and end with `.v1`?"
(every `file://` page shares one drawer; the version lets a later change
ignore old data.)

## What you do

- Review the PR like code. Read `script.js` top to bottom in the diff. Leave
  at least one comment; good candidates: ask him to explain the
  `remembered === "done" || remembered === "todo"` check in a comment, or
  ask what happens if `JSON.parse` throws. Accept small differences from the
  answer key (`var`, arrow functions, a `DOMContentLoaded` wrapper) as long as
  the What to check list holds. Do not accept `fetch`, modules, an unprefixed
  key, or the checkbox state living anywhere but the attribute.
- The break-it exercise, at the checkpoint, on his laptop:
  1. `git switch -c break-the-script`; in `setUpChapters`, change
     `querySelectorAll` to `querySelectorAl`. Save, refresh `index.html`.
  2. Console: `Uncaught TypeError: list.querySelectorAl is not a function`.
     No checkboxes, no bar. But the drawn boxes are back and the counter
     says 8 of 12: the `:has()` rule and the attributes did that. Name it:
     graceful degradation.
  3. Commit, push, `gh pr create --fill`. Watch gitleaks go green on a broken
     site. Say chapter 2's line back to him: a green check that verified
     nothing. Chapter 8 is the answer.
  4. Fix the typo in a second commit, push; Files changed is now empty.
     Close the PR unmerged and delete the branch: nothing to ship. Then
     talk about why the feature and the attribute were two commits.
- Out of sight: nothing new. From chapter 8 you change the Pages build
  settings; not yet.

## Common mistakes

- **The AI reaches for `type="module"` / `fetch("progress.json")` / a JSON
  file.** The chapter predicts it. If it got through, it works on the live
  site and not from `file://`; have him open both.
- **Script tag in `<head>`.** `null` in step 2; "cannot read properties of
  null" later. Move it.
- **Wrong path on the Library page** (`script.js` instead of
  `../script.js`). Nothing happens, no error in the console until you
  scroll; the Network tab shows the 404-shaped failure.
- **A counter built in JS, ignoring the attributes.** Works, but breaks the
  contract every later chapter relies on. Review comment, not a rejection.
- **Whole-file rewrite by the AI.** Reject; ask again naming the function.
- **`:has()` rule missing.** Two boxes per line. Easy to spot.
- **He edits the attribute and the feature in one commit.** Not wrong; ask
  him to split it next time and show `git log` for the reason.
- **Ticks made on the laptop are expected to appear on the phone.** That is
  the lesson, not a bug; do not let him "fix" it.

## Answer key

The one-attribute diff (second commit, `docs: mark chapter 7 done`):

```
-    <li data-chapter="07" data-status="todo">
+    <li data-chapter="07" data-status="done">
```

The two tags, each the last line inside `<body>`:

`index.html`:

```html
<script src="script.js"></script>
```

`library/index.html`:

```html
<script src="../script.js"></script>
```

Commits on `chapter-7`, in order:

```
feat: checkboxes and a progress bar, remembered by the browser
docs: mark chapter 7 done
```

The exact block appended to the end of `styles.css`:

```css
/* ---- chapter 7: checkboxes and the progress bar --------------------------- */
.chapters li:has(> input[type="checkbox"])::before { display: none; }
.chapters li > input[type="checkbox"] { margin: 0; accent-color: var(--done); }
.library li > input[type="checkbox"] { margin: 0 var(--sp-2) 0 0; accent-color: var(--done); }
.bar {
  height: var(--sp-2);
  margin-top: var(--sp-4);
  border: 1px solid var(--border);
  border-radius: var(--radius-pill);
  background: var(--field);
  overflow: hidden;
}
.bar-fill {
  height: 100%;
  width: 0;
  background: var(--accent);
  border-radius: var(--radius-pill);
}
```

The complete `script.js`:

```js
// script.js, from chapter 7. Loaded by a <script> tag at the very end of
// <body> on index.html and library/index.html, so everything it looks for
// already exists when it runs.
//
// It does three things: puts a checkbox on every chapter and every Library
// item, remembers your ticks in this browser, and draws a progress bar.
// The data-status and data-seen attributes stay the truth for CSS; this
// script reads them, lets you change them, and writes them back.

const PROGRESS_KEY = "workbook.progress.v1";
const SEEN_KEY = "workbook.seen.v1";

// ---- remembering ----------------------------------------------------------
// localStorage stores strings only, so objects go in and out as JSON text.

function load(key) {
  try {
    const text = localStorage.getItem(key);
    const value = text ? JSON.parse(text) : {};
    return value && typeof value === "object" ? value : {};
  } catch (error) {
    console.warn("Could not read " + key, error);
    return {};
  }
}

function save(key, value) {
  try {
    localStorage.setItem(key, JSON.stringify(value));
  } catch (error) {
    console.warn("Could not save " + key, error);
  }
}

// ---- one checkbox, at the start of a list item ----------------------------

function addCheckbox(li, checked, onChange) {
  const box = document.createElement("input");
  box.type = "checkbox";
  box.checked = checked;
  box.title = "Remembered by this browser only";
  box.addEventListener("change", function () {
    onChange(box.checked);
  });
  li.prepend(box);
  return box;
}

// ---- the chapter list (index.html) ----------------------------------------

function countDone(items) {
  let done = 0;
  for (const li of items) {
    if (li.dataset.status === "done") {
      done = done + 1;
    }
  }
  return done;
}

function drawBar(fill, items) {
  const percent = items.length === 0 ? 0 : Math.round((countDone(items) / items.length) * 100);
  fill.style.width = percent + "%";
}

function setUpChapters() {
  const list = document.querySelector(".chapters");
  if (!list) {
    return; // not this page
  }
  const items = list.querySelectorAll("li[data-chapter]");
  const saved = load(PROGRESS_KEY);

  const bar = document.createElement("div");
  bar.className = "bar";
  const fill = document.createElement("div");
  fill.className = "bar-fill";
  bar.append(fill);
  list.after(bar);

  for (const li of items) {
    const id = li.dataset.chapter;
    const remembered = saved[id];
    if (remembered === "done" || remembered === "todo") {
      li.dataset.status = remembered; // this browser's note beats the file
    }
    addCheckbox(li, li.dataset.status === "done", function (ticked) {
      li.dataset.status = ticked ? "done" : "todo";
      saved[id] = li.dataset.status;
      save(PROGRESS_KEY, saved);
      drawBar(fill, items);
    });
  }
  drawBar(fill, items);
}

// ---- the Library (library/index.html) -------------------------------------

function setUpLibrary() {
  const items = document.querySelectorAll(".library li[data-seen]");
  if (items.length === 0) {
    return; // not this page
  }
  const saved = load(SEEN_KEY);

  for (const li of items) {
    const link = li.querySelector("a");
    if (!link) {
      continue;
    }
    const id = link.getAttribute("href"); // the address is the item's name
    const remembered = saved[id];
    if (remembered === "yes" || remembered === "no") {
      li.dataset.seen = remembered;
    }
    addCheckbox(li, li.dataset.seen === "yes", function (ticked) {
      li.dataset.seen = ticked ? "yes" : "no";
      saved[id] = li.dataset.seen;
      save(SEEN_KEY, saved);
    });
  }
}

setUpChapters();
setUpLibrary();
```

Behaviour to verify against, from the scaffold session's test of this exact
file: on `index.html` with all `todo` and empty storage it adds twelve
checkboxes as the first child of each `<li>`, a `.bar > .bar-fill` directly
after the list at width `0%`; ticking chapter 7 sets `data-status="done"`,
stores `{"07":"done"}`, and sets the width to `8%`; with chapters 0 to 6
done in the file and `{"07":"done"}` stored, eight are ticked and the width
is `67%`. Corrupt or non-object storage is ignored with one console warning.
On `library/index.html` it adds eleven checkboxes keyed by each link's
`href` and no bar. On a chapter page it adds nothing and throws nothing.
