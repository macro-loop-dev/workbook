# Chapter 8: reviewer notes

Two checkpoints. The first (after 8a) is short and is mostly you looking at
`dist`; the second is the first time he reads a red CI log. Budget 45 minutes
each. Everything below was verified on 2026-09-02 with the pinned versions
(vite 6.4.3, typescript 5.9.3, @biomejs/biome 2.5.11, tsx 4.23.13, marked
18.0.11, @types/node 22.20.1) on Node 22.

## What to check

Mid-checkpoint (after 8a):

- `npm run dev` serves `/`, `/chapters/08-build-step.html`,
  `/library/index.html`, `/notes/`, `/notes/git.html`. A wrong note URL is a
  404, not a copy of the home page (`appType: "mpa"` in `vite.config.ts`).
- `npm run build` finishes; `dist` has `index.html`, all twelve chapter pages,
  `library/index.html`, `notes/index.html` plus one page per note,
  `styles.css` at the root, and `assets/` with one `.js` and one `.css`.
- `git status` after his commit: `node_modules` and `dist` absent;
  `package-lock.json` present; `script.js` and every `notes/*.html` deleted.
- `src/progress.json` says `00`-`07` done, `08`-`11` todo. If he ticked more
  in chapter 7, that lives in localStorage, not here; the JSON is the
  committed truth.
- `library.json` has one object per item that was in `library/index.html`,
  including anything you shared after 2026-09-02. Every `id` unique.

End checkpoint:

- `npm run typecheck; npm run lint; npm test; npm run build` all green
  locally.
- The PR shows two checks, `secrets` and `checks`, both green.
- The last commit on the branch is `docs: mark chapter 8 done` and touches one
  line.

## What to ask

- "What is `package-lock.json` for?" Good: it records the exact versions so
  `npm ci` on another machine installs the same thing. Better: that is why CI
  can be trusted to agree with his laptop.
- "What does `: Status` mean?" Good: the value can only be `"todo"` or
  `"done"`. Then have him type `status[id] = "finished"` in `main.ts` and read
  the red underline aloud. The checker refused before anything ran.
- "Why does `progress.ts` not read `progress.json`?" The answer he should
  reach: in chapter 9 the data moves to a server and this file stays the same.
- "Why did the CSS counter keep working when the list became data?" Because
  `main.ts` still sets `data-status`; the CSS never learned anything changed.
- "Where did `file://` go?" `type="module"` and `fetch` need a server. The
  server is `npm run dev` now and Cloudflare later.

## What you do

- **Mid-checkpoint:** look at the `dist` listing with him. Nothing else.
- **Before he merges:** Cloudflare dashboard, this project -> Settings ->
  Builds: build command `npm run build`, output directory `dist`, and an
  environment variable `NODE_VERSION` = `22` for production and preview.
  `.node-version` should be enough alone; the variable is belt and braces.
  Do this while the PR is open, so his merge is the first build.
- **The failing test.** On your machine, in the workbook clone:

  ```powershell
  git switch main; git pull --ff-only; git switch -c william/broken-test
  ```

  In `src/chapters.test.ts` change the line
  `ok("twelve chapters in the manifest", CHAPTERS.length === 12);` to
  `=== 13`. Commit `test: break something on purpose`, push, open a PR, and
  hand him the red check. He finds `FAIL: twelve chapters in the manifest` in
  the log, opens the file, works out which side of the `===` is wrong, fixes
  it on your branch, pushes, watches it go green. Close the PR without merging
  and delete the branch. Do not tell him which file.
- Review the real PR as you would anyone's: a comment on the commit message
  if it lacks a prefix, a comment if `package-lock.json` is missing.

## Common mistakes

- Installing in the blue terminal: "npm.ps1 cannot be loaded". Icon first.
- `npm i` without `-D`: the packages land under `dependencies`. Harmless
  here, but the full `package.json` in the answer key has them under
  `devDependencies`; when he pastes it, versions must match what he installed
  or `npm ci` refuses.
- Editing `package.json` by hand after installing, then `npm ci` failing in
  CI with "lock file out of sync". Fix: `npm install`, commit the lock.
- Forgetting `git rm script.js` or leaving `<script src="script.js">` in
  `index.html`: the console shows a 404 for it and the list renders twice or
  not at all, depending on what chapter 7's script does with an empty list.
- Leaving `notes/index.html` in place: `notes.test.ts` fails on purpose
  ("no .html left in notes/").
- A chapter saved as `.htm` or outside `chapters/`: silently absent from
  `dist`. The gotcha in the chapter covers it.
- Biome reformatting a file he did not touch after `lint:fix`: with
  `includes` limited to `src/**`, `*.ts` and `*.json` that should not happen;
  if it does, the file was already unformatted, and the diff is still worth
  reading.
- The AI adding Vitest "for a better test runner". Reject; rule 5.

## Answer key

Every file in full. Line endings are LF (Cursor's setting); Biome formats
with two spaces and a 100-column limit, so `npm run lint:fix` should change
nothing if he typed these exactly.

### `package.json`

```json
{
  "name": "workbook",
  "private": true,
  "version": "0.8.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc --noEmit && vite build",
    "preview": "vite preview",
    "typecheck": "tsc --noEmit",
    "lint": "biome check .",
    "lint:fix": "biome check --write .",
    "test": "tsx src/progress.test.ts && tsx src/chapters.test.ts && tsx src/library.test.ts && tsx src/notes.test.ts"
  },
  "devDependencies": {
    "@biomejs/biome": "^2.5.11",
    "@cloudflare/workers-types": "^5.20260902.1",
    "@types/node": "^22.20.1",
    "marked": "^18.0.11",
    "tsx": "^4.23.13",
    "typescript": "^5.9.3",
    "vite": "^6.4.3",
    "wrangler": "^4.128.0"
  }
}
```

### `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "skipLibCheck": true,
    "types": ["node"],

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,

    /* Linting */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src", "vite.config.ts"]
}
```

### `biome.json`

```json
{
  "$schema": "https://biomejs.dev/schemas/2.5.11/schema.json",
  "files": {
    "includes": ["src/**", "*.ts", "*.json", "!package-lock.json"]
  },
  "formatter": {
    "enabled": true,
    "indentStyle": "space",
    "indentWidth": 2,
    "lineWidth": 100
  },
  "javascript": {
    "formatter": {
      "quoteStyle": "double"
    }
  },
  "linter": {
    "enabled": true,
    "rules": {
      "preset": "recommended"
    }
  }
}
```

### `.node-version`

```
22
```

### `vite.config.ts`

```ts
import { readdirSync, readFileSync } from "node:fs";
import { marked } from "marked";
import { defineConfig, type Plugin } from "vite";

// Vite bundles only the pages it is told about. Every chapter is a page.
const pages: Record<string, string> = { index: "index.html", library: "library/index.html" };
for (const f of readdirSync("chapters")) {
  if (f.endsWith(".html")) pages[f.slice(0, -5)] = `chapters/${f}`;
}

// ---- notes: Markdown in, pages out --------------------------------------
// notes/*.md become notes/*.html, on the dev server and in dist. A file whose
// name starts with _ is a template and is skipped.
interface Note {
  name: string;
  title: string;
  date: string;
  body: string;
}

function readNotes(): Note[] {
  return readdirSync("notes")
    .filter((f) => f.endsWith(".md") && !f.startsWith("_"))
    .map((f) => {
      const name = f.slice(0, -3);
      const raw = readFileSync(`notes/${f}`, "utf8");
      const m = /^---\r?\n([\s\S]*?)\r?\n---\r?\n?([\s\S]*)$/.exec(raw);
      const head: Record<string, string> = {};
      for (const line of (m?.[1] ?? "").split(/\r?\n/)) {
        const i = line.indexOf(":");
        if (i > 0) head[line.slice(0, i).trim()] = line.slice(i + 1).trim();
      }
      return { name, title: head.title ?? name, date: head.date ?? "", body: m?.[2] ?? raw };
    })
    .sort((a, b) => b.date.localeCompare(a.date) || a.name.localeCompare(b.name));
}

const esc = (s: string) => s.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");

function page(title: string, main: string): string {
  return `<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>${esc(title)} · Notes</title>
<link rel="stylesheet" href="../styles.css">
</head>
<body>
<div class="page">
  <nav class="site-nav" aria-label="Site">
    <a class="brand" href="../index.html">The Workbook</a>
    <a href="../index.html">Chapters</a>
    <a href="../library/index.html">Library</a>
    <a href="index.html" aria-current="page">Notes</a>
  </nav>
${main}
  <nav class="pager"><a href="index.html">&larr; all notes</a></nav>
</div>
</body>
</html>
`;
}

function renderNote(n: Note): string {
  const header = `  <header class="chapter-header"><div class="kicker"><span>${esc(n.date)}</span></div><h1>${esc(n.title)}</h1></header>\n`;
  return page(n.title, header + marked.parse(n.body, { async: false }));
}

function renderIndex(notes: Note[]): string {
  const items = notes
    .map(
      (n) =>
        `    <li><span class="date">${esc(n.date)}</span><a href="${n.name}.html">${esc(n.title)}</a></li>`,
    )
    .join("\n");
  return page(
    "Notes",
    `  <h1>Notes</h1>\n  <p class="lede">Yours. Nobody edits these but you.</p>\n  <ul class="notes-list">\n${items}\n  </ul>\n`,
  );
}

function notesPlugin(): Plugin {
  const html = (name: string): string | null => {
    const notes = readNotes();
    if (name === "index") return renderIndex(notes);
    const n = notes.find((x) => x.name === name);
    return n ? renderNote(n) : null;
  };
  return {
    name: "workbook-notes",
    configureServer(server) {
      server.middlewares.use((req, res, next) => {
        const m = /^\/notes\/(?:([^/?]+)\.html)?(?:\?.*)?$/.exec(req.url ?? "");
        const out = m ? html(m[1] ?? "index") : null;
        if (out === null) return next();
        res.setHeader("Content-Type", "text/html; charset=utf-8");
        res.end(out);
      });
    },
    generateBundle() {
      for (const name of ["index", ...readNotes().map((n) => n.name)]) {
        this.emitFile({ type: "asset", fileName: `notes/${name}.html`, source: html(name) ?? "" });
      }
      // The notes pages link ../styles.css by name; Vite copies nothing it is
      // not told about, so tell it.
      this.emitFile({
        type: "asset",
        fileName: "styles.css",
        source: readFileSync("styles.css", "utf8"),
      });
    },
  };
}

export default defineConfig({
  appType: "mpa",
  plugins: [notesPlugin()],
  build: { rollupOptions: { input: pages } },
});
```

### `src/progress.ts`

```ts
// The vocabulary of progress. No DOM, no JSON, no fetch: pure functions that
// the page, the tests and (from chapter 9) the server all share.

export const CHAPTER_IDS = [
  "00",
  "01",
  "02",
  "03",
  "04",
  "05",
  "06",
  "07",
  "08",
  "09",
  "10",
  "11",
] as const;

export type ChapterId = (typeof CHAPTER_IDS)[number];
export type Status = "todo" | "done";

export function isStatus(x: unknown): x is Status {
  return x === "todo" || x === "done";
}

export function isChapterId(x: unknown): x is ChapterId {
  return typeof x === "string" && (CHAPTER_IDS as readonly string[]).includes(x);
}

export function countDone(chapters: Record<string, Status>): number {
  return Object.values(chapters).filter((s) => s === "done").length;
}
```

### `src/progress.json`

```json
{
  "version": 1,
  "chapters": {
    "00": "done",
    "01": "done",
    "02": "done",
    "03": "done",
    "04": "done",
    "05": "done",
    "06": "done",
    "07": "done",
    "08": "todo",
    "09": "todo",
    "10": "todo",
    "11": "todo"
  }
}
```

### `src/chapters.ts`

```ts
// The manifest: one entry per chapter page. The list on index.html is drawn
// from this, and chapters.test.ts checks it against the files on disk.

export interface Chapter {
  number: number;
  slug: string;
  title: string;
  mechanic: string;
  time: string;
}

export const CHAPTERS: readonly Chapter[] = [
  { number: 0, slug: "00-setup", title: "Set up the machine", mechanic: "watch", time: "2-3 h" },
  {
    number: 1,
    slug: "01-edit-and-refresh",
    title: "Edit the file, refresh the browser",
    mechanic: "edit + refresh",
    time: "1 h",
  },
  {
    number: 2,
    slug: "02-ask-the-ai",
    title: "Ask the AI, review the diff",
    mechanic: "ask the AI",
    time: "1-2 h",
  },
  {
    number: 3,
    slug: "03-commit",
    title: "Commit in the terminal",
    mechanic: "commit",
    time: "2 h",
  },
  {
    number: 4,
    slug: "04-push-and-pr",
    title: "Push and open a pull request",
    mechanic: "push + PR",
    time: "2 h",
  },
  {
    number: 5,
    slug: "05-merge-and-ship",
    title: "Merge and ship",
    mechanic: "merge = deploy",
    time: "1 h",
  },
  {
    number: 6,
    slug: "06-your-domain",
    title: "A domain of your own",
    mechanic: "your domain",
    time: "1-2 h",
  },
  {
    number: 7,
    slug: "07-javascript",
    title: "JavaScript makes it interactive",
    mechanic: "JavaScript",
    time: "3-4 h",
  },
  {
    number: 8,
    slug: "08-build-step",
    title: "A build step",
    mechanic: "build + tests",
    time: "4-6 h",
  },
  {
    number: 9,
    slug: "09-server-side",
    title: "Save progress on the server",
    mechanic: "server + database",
    time: "6-8 h",
  },
  {
    number: 10,
    slug: "10-portfolio",
    title: "Restructure into a portfolio",
    mechanic: "portfolio",
    time: "4-6 h",
  },
  {
    number: 11,
    slug: "11-floatnote-pr",
    title: "A real pull request to FloatNote",
    mechanic: "FloatNote PR",
    time: "4-8 h",
  },
];

/** "08" for chapter 8: the key used in progress.json and on data-chapter. */
export function chapterId(c: Chapter): string {
  return String(c.number).padStart(2, "0");
}
```

### `src/library.ts`

```ts
// The vocabulary of the Library. The items themselves live in library.json.

export const CATEGORIES = ["sites-apps", "tools", "prompting", "reading"] as const;

export type Category = (typeof CATEGORIES)[number];

export interface LibraryItem {
  id: string;
  category: Category;
  title: string;
  url: string;
  why: string;
  added: string;
  seen: boolean;
}

export function isCategory(x: unknown): x is Category {
  return typeof x === "string" && (CATEGORIES as readonly string[]).includes(x);
}
```

### `src/library.json`

The eleven seeded items. Anything you shared after 2026-09-02 is one more
object each, in the shape the `share-library` skill writes.

```json
{
  "version": 1,
  "items": [
    {
      "id": "floatnote",
      "category": "sites-apps",
      "title": "FloatNote",
      "url": "https://app.floatnote.dev",
      "why": "The app you will send a pull request to in chapter 11. Type some maths in it and watch the answer column.",
      "added": "2026-09-02",
      "seen": false
    },
    {
      "id": "ciechanowski",
      "category": "sites-apps",
      "title": "Bartosz Ciechanowski's explainers",
      "url": "https://ciechanow.ski/",
      "why": "Interactive explanations of how things work (GPS, cameras, bicycles). Every one is hand-built web code with no framework, which is the kind you are learning.",
      "added": "2026-09-02",
      "seen": false
    },
    {
      "id": "neal-fun",
      "category": "sites-apps",
      "title": "neal.fun",
      "url": "https://neal.fun/",
      "why": "Small, playful web toys. Proof that a website can be one idea done well.",
      "added": "2026-09-02",
      "seen": false
    },
    {
      "id": "cursor-docs",
      "category": "tools",
      "title": "Cursor docs",
      "url": "https://docs.cursor.com/",
      "why": "The editor you are using. Read the pages on Tab, Chat and Rules; the rest can wait.",
      "added": "2026-09-02",
      "seen": false
    },
    {
      "id": "github-cli",
      "category": "tools",
      "title": "GitHub CLI",
      "url": "https://cli.github.com/",
      "why": "The gh command. From chapter 4 you open pull requests with it instead of clicking.",
      "added": "2026-09-02",
      "seen": false
    },
    {
      "id": "excalidraw",
      "category": "tools",
      "title": "Excalidraw",
      "url": "https://excalidraw.com/",
      "why": "Draw a diagram before you build a thing. Free, no account, works in the browser.",
      "added": "2026-09-02",
      "seen": false
    },
    {
      "id": "willison-llms-for-code",
      "category": "prompting",
      "title": "Simon Willison: how I use LLMs to help me write code",
      "url": "https://simonwillison.net/2025/Mar/11/using-llms-for-code/",
      "why": "The honest version. Set expectations, give context, test what it gives you. Chapter 2 in someone else's words.",
      "added": "2026-09-02",
      "seen": false
    },
    {
      "id": "anthropic-prompt-engineering",
      "category": "prompting",
      "title": "Anthropic: prompt engineering overview",
      "url": "https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview",
      "why": "Be clear, give examples, let it think. Written for people building with the API, but the first two pages apply to any chat box.",
      "added": "2026-09-02",
      "seen": false
    },
    {
      "id": "mdn-learn",
      "category": "reading",
      "title": "MDN: Learn web development",
      "url": "https://developer.mozilla.org/en-US/docs/Learn_web_development",
      "why": "The reference for HTML, CSS and JavaScript. When the AI tells you what a tag does, this is where you check.",
      "added": "2026-09-02",
      "seen": false
    },
    {
      "id": "missing-semester",
      "category": "reading",
      "title": "The Missing Semester of Your CS Education",
      "url": "https://missing.csail.mit.edu/",
      "why": "A course on the terminal, git and the tools around code. Chapter 3 and 4 material, taught by MIT for free.",
      "added": "2026-09-02",
      "seen": false
    },
    {
      "id": "wizard-zines",
      "category": "reading",
      "title": "Julia Evans' zines",
      "url": "https://wizardzines.com/",
      "why": "Comics that explain git, the terminal and networking. Read the free ones first.",
      "added": "2026-09-02",
      "seen": false
    }
  ]
}
```

### `src/main.ts`

```ts
// The page's only script. Draws the chapter list and the Library from the
// files in src/, then layers on what this browser remembers (chapter 7).
import { CHAPTERS, chapterId } from "./chapters";
import { isCategory } from "./library";
import libraryData from "./library.json";
import { countDone, isStatus, type Status } from "./progress";
import progressData from "./progress.json";

// Same two keys as chapter 7. The prefix matters: every page you ever open
// on this computer's localhost shares one localStorage.
const PROGRESS_KEY = "workbook.progress.v1";
const SEEN_KEY = "workbook.seen.v1";

function readOverlay(key: string): Record<string, string> {
  try {
    const parsed: unknown = JSON.parse(localStorage.getItem(key) ?? "{}");
    if (parsed && typeof parsed === "object" && !Array.isArray(parsed)) {
      return parsed as Record<string, string>;
    }
  } catch {
    // not JSON: it is a cache, not the truth, so start again
  }
  return {};
}

function writeOverlay(key: string, value: Record<string, string>): void {
  localStorage.setItem(key, JSON.stringify(value));
}

function make<K extends keyof HTMLElementTagNameMap>(
  tag: K,
  className = "",
  text = "",
): HTMLElementTagNameMap[K] {
  const node = document.createElement(tag);
  if (className) node.className = className;
  if (text) node.textContent = text;
  return node;
}

function renderChapters(list: HTMLOListElement): void {
  const committed: Record<string, string> = progressData.chapters;
  const overlay = readOverlay(PROGRESS_KEY);
  const status: Record<string, Status> = {};
  for (const c of CHAPTERS) {
    const id = chapterId(c);
    const s = overlay[id] ?? committed[id];
    status[id] = isStatus(s) ? s : "todo";
  }

  const bar = make("div", "bar");
  const fill = make("div", "bar-fill");
  bar.append(fill);
  const paint = () => {
    fill.style.width = `${Math.round((100 * countDone(status)) / CHAPTERS.length)}%`;
  };

  for (const c of CHAPTERS) {
    const id = chapterId(c);
    const li = make("li");
    li.dataset.chapter = id;
    li.dataset.status = status[id];
    const box = make("input");
    box.type = "checkbox";
    box.checked = status[id] === "done";
    box.addEventListener("change", () => {
      status[id] = box.checked ? "done" : "todo";
      li.dataset.status = status[id];
      overlay[id] = status[id];
      writeOverlay(PROGRESS_KEY, overlay);
      paint();
    });
    const link = make("a", "", c.title);
    link.href = `chapters/${c.slug}.html`;
    const meta = make("span", "meta");
    meta.append(make("span", "badge", c.mechanic), make("span", "", c.time));
    li.append(make("span", "num", String(c.number)), box, link, meta);
    list.append(li);
  }
  list.after(bar);
  paint();
}

function renderLibrary(): void {
  const overlay = readOverlay(SEEN_KEY);
  for (const item of libraryData.items) {
    if (!isCategory(item.category)) continue;
    const list = document.querySelector<HTMLUListElement>(`ul[data-category="${item.category}"]`);
    if (!list) continue;
    const li = make("li");
    li.dataset.added = item.added;
    li.dataset.seen = item.seen || overlay[item.id] === "yes" ? "yes" : "no";
    const box = make("input");
    box.type = "checkbox";
    box.title = "seen";
    box.checked = li.dataset.seen === "yes";
    box.addEventListener("change", () => {
      li.dataset.seen = box.checked ? "yes" : "no";
      overlay[item.id] = li.dataset.seen;
      writeOverlay(SEEN_KEY, overlay);
    });
    const link = make("a", "", item.title);
    link.href = item.url;
    li.append(box, " ", link, " ", make("span", "why", item.why));
    list.append(li);
  }
}

const chapterList = document.querySelector<HTMLOListElement>("ol#chapters");
if (chapterList) renderChapters(chapterList);
if (document.querySelector("ul[data-category]")) renderLibrary();
```

### `index.html` (the changed part)

The `<ol>` and its twelve `<li>` lines become:

```html
  <ol class="chapters progress" id="chapters"></ol>
```

The chapter-7 `<script src="script.js"></script>` goes; before `</body>`:

```html
<script type="module" src="/src/main.ts"></script>
```

### `library/index.html` (the changed part)

Each of the four lists becomes empty, keeping its attribute, e.g.
`<ul data-category="tools"></ul>`; the same module `<script>` line goes
before `</body>`.

### `notes/_template.md`

```md
---
title: TITLE
date: YYYY-MM-DD
---
<!-- To use: copy this file to YYYY-MM-DD-short-title.md in this folder,
     fill in the two lines above, write under the headings, delete the ones
     you do not need. The index page lists it on the next build. -->

## What I learned

## What confused me

## What I would tell someone starting this
```

### `notes/git.md` (the others follow the same shape)

```md
---
title: git cheat sheet
date: 2026-09-02
---
Commands I actually use, in my own words.

| Command | What it does | When I use it |
| --- | --- | --- |
| `git status` | Lists what changed since the last commit | Before every commit, and whenever I am confused |
```

### `notes/til.md` (shape; his entries will differ)

```md
---
title: Today I learned
date: 2026-09-02
---
One line per chapter, at least. Newest at the top.

- **2026-09-02, example (delete me).** A file on my laptop can be a whole
  website. There is nothing in between.
```

### `src/progress.test.ts`

```ts
// progress.json is data, and data rots: a typo in a status would silently
// stop counting. This pins the shape.

import { CHAPTER_IDS, countDone, isChapterId, isStatus } from "./progress";
import data from "./progress.json";

let pass = 0;
let fail = 0;
function eq(name: string, actual: unknown, expected: unknown) {
  if (JSON.stringify(actual) === JSON.stringify(expected)) {
    pass++;
  } else {
    fail++;
    console.log(`FAIL: ${name}: got ${JSON.stringify(actual)}, want ${JSON.stringify(expected)}`);
  }
}

eq("version is 1", data.version, 1);
eq("one entry per chapter, no extras", Object.keys(data.chapters).sort(), [...CHAPTER_IDS]);
for (const [id, status] of Object.entries(data.chapters)) {
  eq(`chapter ${id} has a real status`, isStatus(status), true);
}

eq("isStatus accepts done", isStatus("done"), true);
eq("isStatus rejects finished", isStatus("finished"), false);
eq("isChapterId accepts 08", isChapterId("08"), true);
eq("isChapterId rejects 8", isChapterId("8"), false);
eq("isChapterId rejects 12", isChapterId("12"), false);
eq("countDone counts only done", countDone({ "00": "done", "01": "todo", "02": "done" }), 2);

console.log(`${pass} passed, ${fail} failed`);
if (fail > 0) process.exit(1);
```

### `src/chapters.test.ts`

```ts
// The manifest and the folder must agree, and every prev/next link at the
// bottom of a chapter must go somewhere. A dead pager link is the kind of
// thing nobody notices until a reader does.

import { existsSync, readdirSync, readFileSync } from "node:fs";
import { join } from "node:path";
import { CHAPTERS, chapterId } from "./chapters";

let pass = 0;
let fail = 0;
function ok(name: string, condition: boolean) {
  if (condition) {
    pass++;
  } else {
    fail++;
    console.log(`FAIL: ${name}`);
  }
}

const dir = join(import.meta.dirname, "..", "chapters");
const files = readdirSync(dir)
  .filter((f) => f.endsWith(".html"))
  .sort();
const slugs = CHAPTERS.map((c) => c.slug);

ok("twelve chapters in the manifest", CHAPTERS.length === 12);
for (const f of files) {
  ok(`${f} is in the manifest`, slugs.includes(f.slice(0, -5)));
}
for (const c of CHAPTERS) {
  ok(`${c.slug}.html exists`, files.includes(`${c.slug}.html`));
  ok(`${c.slug} id matches its number`, chapterId(c) === c.slug.slice(0, 2));
}

for (const f of files) {
  const html = readFileSync(join(dir, f), "utf8");
  const pager = /<nav class="pager"[^>]*>([\s\S]*?)<\/nav>/.exec(html);
  ok(`${f} has a pager`, pager !== null);
  for (const m of (pager?.[1] ?? "").matchAll(/href="([^"]+)"/g)) {
    ok(`${f} links to ${m[1]}`, existsSync(join(dir, m[1])));
  }
}

console.log(`${pass} passed, ${fail} failed`);
if (fail > 0) process.exit(1);
```

### `src/library.test.ts`

```ts
// library.json arrives by pull request, one item at a time. This is the part
// of the review a machine can do.

import { isCategory } from "./library";
import data from "./library.json";

let pass = 0;
let fail = 0;
function ok(name: string, condition: boolean) {
  if (condition) {
    pass++;
  } else {
    fail++;
    console.log(`FAIL: ${name}`);
  }
}

ok("version is 1", data.version === 1);
ok("at least one item", data.items.length > 0);

const ids = new Set<string>();
for (const item of data.items) {
  const label = `item ${JSON.stringify(item.id)}`;
  ok(`${label} has an id`, typeof item.id === "string" && item.id.length > 0);
  ok(`${label} id is unique`, !ids.has(item.id));
  ids.add(item.id);
  ok(`${label} has a known category`, isCategory(item.category));
  ok(`${label} has a title`, item.title.trim().length > 0);
  ok(`${label} url is https`, item.url.startsWith("https://"));
  ok(`${label} has a why`, item.why.trim().length > 0);
  ok(`${label} added is a date`, /^\d{4}-\d{2}-\d{2}$/.test(item.added));
  ok(`${label} seen is true or false`, typeof item.seen === "boolean");
}

console.log(`${pass} passed, ${fail} failed`);
if (fail > 0) process.exit(1);
```

### `src/notes.test.ts`

```ts
// Every note is Markdown with a title and a date at the top. The build turns
// them into pages; a note without front matter would get a blank title and
// sort to the bottom without anyone being told.

import { readdirSync, readFileSync } from "node:fs";
import { join } from "node:path";

let pass = 0;
let fail = 0;
function ok(name: string, condition: boolean) {
  if (condition) {
    pass++;
  } else {
    fail++;
    console.log(`FAIL: ${name}`);
  }
}

const dir = join(import.meta.dirname, "..", "notes");
const files = readdirSync(dir);

ok("no .html left in notes/ (the build makes those)", !files.some((f) => f.endsWith(".html")));

for (const f of files.filter((x) => x.endsWith(".md") && !x.startsWith("_"))) {
  const raw = readFileSync(join(dir, f), "utf8");
  const m = /^---\r?\n([\s\S]*?)\r?\n---\r?\n/.exec(raw);
  ok(`${f} starts with front matter`, m !== null);
  const head = m?.[1] ?? "";
  ok(`${f} has a title`, /^title: \S/m.test(head));
  ok(`${f} has a date like 2026-09-02`, /^date: \d{4}-\d{2}-\d{2}$/m.test(head));
}

console.log(`${pass} passed, ${fail} failed`);
if (fail > 0) process.exit(1);
```

### `.github/workflows/gates.yml` (the added job)

```yaml
  checks:
    # The slow gates. Same five commands you run before every commit, on a
    # machine that has never seen your laptop.
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm
      - run: npm ci
      - run: npm run typecheck
      - run: npm run lint
      - run: npm test
      - run: npm run build
```

### The mark-it-done diff

```
-    "08": "todo",
+    "08": "done",
```

Expected test output after that: `20 passed, 0 failed`, `73 passed, 0 failed`,
`90 passed, 0 failed`, `19 passed, 0 failed` (the chapter count depends on
how many library items there are; the last number changes with his notes).
