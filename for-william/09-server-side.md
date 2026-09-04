# Chapter 9: reviewer notes

The heaviest chapter. It splits naturally: steps 1-7 (wrangler, D1, the
migration, the hand-typed UPDATE) one sitting; 8-16 (Functions, main.ts)
another; 17-19 and the checkpoint a third. The Functions and the client
were run end to end on 2026-09-02 under `wrangler pages dev` with a local
D1 (wrangler 4.128.0, @cloudflare/workers-types 5.20260902.1, workerd
1.20260831): GET, PUT without a key (401), wrong key (401), right key
(200), bad status (400), bad chapter (404), seen true/false. The remote
half (`--remote`, the dashboard secret, the live deploy) could not be run
here; the commands are the documented wrangler 4 forms.

## What to check

- `wrangler.toml` has a real `database_id` and the `[[d1_databases]]` block.
  The dashboard's Functions bindings page shows D1 `DB` as read-only,
  sourced from the file.
- `git check-ignore .dev.vars` prints the name on his laptop. `git log --all
  -- .dev.vars` prints nothing.
- `npx wrangler d1 execute workbook --remote --command "SELECT * FROM
  progress"`: twelve rows, `00`-`08` done. `SELECT * FROM seen` empty until
  he ticks something.
- `Invoke-RestMethod https://<domain>/api/progress` from your machine
  returns the same rows. A PUT without the header is a 401. A PUT to
  `/api/progress/12` with the key is a 404. A body of `{"status":"soon"}`
  is a 400.
- CI: both jobs green, and the `checks` job log shows
  `typecheck:functions` running.
- `src/progress.json` and `src/progress.test.ts` are gone; `src/progress.ts`
  is byte-identical to chapter 8's.

## What to ask

- "Where is the key?" He should name three places and no fourth: the
  dashboard secret, his `.dev.vars`, his browser's localStorage. Not the
  repo, not `main.ts`, not the PR description.
- "What is the difference between 401 and 403?" 401: who are you; 403: I
  know, and no.
- "Why does `--local` exist?" So a mistake in SQL breaks a copy. Follow-up:
  "which one did you just run that against?" until he answers before you
  finish asking.
- "Why is there no key check on GET?" Public site, public scoreboard.
- "What happens if you delete localStorage?" The page asks for the key
  again; nothing else changes, because nothing else was there.
- "What does `chapter <= '08'` compare?" Text. Works because ids are
  two digits. Ask what `'9' <= '10'` would say (false: `'9'` sorts after
  `'1'`). That is the whole reason for the zero padding since chapter 1.

## What you do

- **Before the session:** either create D1 `workbook` in the dashboard
  (Storage & Databases -> D1) or let him run `npx wrangler d1 create`. Both
  fine; the id goes in the file either way.
- **Before he merges:** Workers & Pages -> the project -> Settings ->
  Variables and Secrets: `WORKBOOK_KEY`, type Secret, production and
  preview. Generate it with `[guid]::NewGuid().ToString("N")`. Give it to
  him in person or in a message that you both delete; not in a PR comment.
- **Review the Functions line by line.** The things worth a comment if they
  drift from the answer key: the key check before anything else; the 500
  when the secret is unset (so a misconfiguration does not look like a bad
  key); `isChapterId`/`isStatus` imported from `src`, not re-typed; every
  error branch returning JSON with a sentence.
- **The planted moment.** Have him ask the AI, in Cursor, "make the key not
  prompt every time". Watch it either hardcode the key or move it into a
  file. He rejects. If it instead suggests a query parameter or a cookie,
  that is a better AI than expected; still reject, and talk about why a key
  in a URL ends up in logs.
- **Rotate.** Change the secret in the dashboard. Have him tick anything.
  He should see the alert, then the prompt. Give him the new key. Then
  check the old one is really dead with a PUT from your machine.
- Optional: `npx wrangler pages deployment tail` on your machine while he
  clicks, so he sees his requests arrive.

## Common mistakes

- Migration applied `--local` only; live site returns an empty object or a
  D1 error. Look at the deployment tail.
- `wrangler.toml` committed without the D1 block, or with the block added in
  a later commit than the Functions: the first deploy has Functions and no
  database.
- `.dev.vars` committed. gitleaks may not catch a 32-hex string with no
  prefix. The `.gitignore` entry has been there since chapter 0 for this
  reason; `git check-ignore` is the test. If it does get committed, rotate
  the local key (it is only local) and rewrite nothing; a local key is not a
  secret worth history surgery.
- Pasting the local key into the live site (the story's mistake in
  reverse). The page clears it and asks again.
- Running `npx wrangler pages dev dist` without building first: old `dist`.
- `Invoke-RestMethod` "cannot bind parameter Headers": the hashtable syntax
  `@{ "X-Workbook-Key" = "..." }` needs the spaces and the `=`.
- Trying `npm run dev` and reporting "ticks do not save": correct behaviour;
  the console warning names `dev:full`.
- The AI inventing a `functions/_middleware.ts` for CORS or auth. Not
  needed; reject.

## Answer key

### `wrangler.toml`

The id is his; everything else exact.

```toml
# Cloudflare settings that live in the repo. Once this file names
# pages_build_output_dir, bindings come from here and the dashboard only
# shows them. An id is not a secret; the key is, and it is not in here.
name = "workbook"
compatibility_date = "2026-08-25"
pages_build_output_dir = "dist"

[[d1_databases]]
binding = "DB"
database_name = "workbook"
database_id = "paste-the-id-that-wrangler-d1-create-printed"
```

### `migrations/0001_progress.sql`

```sql
-- Chapter 9. Two tables: one row per chapter, one row per Library item you
-- have seen. A migration runs once per database and is never edited after
-- it has run; the next change is 0002_something.sql.
CREATE TABLE progress (
  chapter TEXT PRIMARY KEY,
  status TEXT NOT NULL,
  updated_at TEXT NOT NULL
);

INSERT INTO progress (chapter, status, updated_at) VALUES
  ('00', 'todo', '2026-09-02T00:00:00Z'),
  ('01', 'todo', '2026-09-02T00:00:00Z'),
  ('02', 'todo', '2026-09-02T00:00:00Z'),
  ('03', 'todo', '2026-09-02T00:00:00Z'),
  ('04', 'todo', '2026-09-02T00:00:00Z'),
  ('05', 'todo', '2026-09-02T00:00:00Z'),
  ('06', 'todo', '2026-09-02T00:00:00Z'),
  ('07', 'todo', '2026-09-02T00:00:00Z'),
  ('08', 'todo', '2026-09-02T00:00:00Z'),
  ('09', 'todo', '2026-09-02T00:00:00Z'),
  ('10', 'todo', '2026-09-02T00:00:00Z'),
  ('11', 'todo', '2026-09-02T00:00:00Z');

CREATE TABLE seen (
  item_id TEXT PRIMARY KEY,
  seen_at TEXT NOT NULL
);
```

### The one hand-typed statement

```
npx wrangler d1 execute workbook --remote --command "UPDATE progress SET status = 'done' WHERE chapter <= '08'"
```

Expected: 9 rows written. Then the same with `--local`.

### `functions/api/progress.ts`

```ts
// GET /api/progress
// -> { "chapters": { "00": "done", ... }, "seen": ["floatnote", ...] }
// Reading needs no key: the site is public, so the scoreboard is too.

interface Env {
  DB: D1Database;
}

export const onRequestGet: PagesFunction<Env> = async ({ env }) => {
  const progress = await env.DB.prepare(
    "SELECT chapter, status FROM progress ORDER BY chapter",
  ).all<{ chapter: string; status: string }>();
  const chapters: Record<string, string> = {};
  for (const row of progress.results) chapters[row.chapter] = row.status;

  const seen = await env.DB.prepare("SELECT item_id FROM seen").all<{ item_id: string }>();
  return Response.json({ chapters, seen: seen.results.map((row) => row.item_id) });
};
```

### `functions/api/progress/[chapter].ts`

```ts
// PUT /api/progress/08   header X-Workbook-Key: <the key>   body {"status":"done"}
// -> { "chapter": "08", "status": "done", "updated_at": "2026-..." }
// The file name is the route: [chapter] means "whatever came after /progress/".
import { isChapterId, isStatus } from "../../../src/progress";

interface Env {
  DB: D1Database;
  WORKBOOK_KEY?: string;
}

export const onRequestPut: PagesFunction<Env, "chapter"> = async ({ request, env, params }) => {
  // The secret comes from the environment, never from a file in the repo.
  if (!env.WORKBOOK_KEY) {
    return Response.json({ error: "WORKBOOK_KEY is not set on the server" }, { status: 500 });
  }
  if (request.headers.get("X-Workbook-Key") !== env.WORKBOOK_KEY) {
    return Response.json({ error: "unauthorized" }, { status: 401 });
  }

  const chapter = params.chapter;
  if (!isChapterId(chapter)) {
    return Response.json({ error: "no such chapter" }, { status: 404 });
  }
  const body = (await request.json().catch(() => null)) as { status?: unknown } | null;
  const status = body?.status;
  if (!isStatus(status)) {
    return Response.json({ error: 'status must be "todo" or "done"' }, { status: 400 });
  }

  const updatedAt = new Date().toISOString();
  await env.DB.prepare("UPDATE progress SET status = ?1, updated_at = ?2 WHERE chapter = ?3")
    .bind(status, updatedAt, chapter)
    .run();
  return Response.json({ chapter, status, updated_at: updatedAt });
};
```

### `functions/api/seen/[id].ts`

```ts
// PUT /api/seen/floatnote   header X-Workbook-Key: <the key>   body {"seen":true}
// -> { "item_id": "floatnote", "seen": true }
// true adds a row, false removes it; the table only lists what you have seen.

interface Env {
  DB: D1Database;
  WORKBOOK_KEY?: string;
}

export const onRequestPut: PagesFunction<Env, "id"> = async ({ request, env, params }) => {
  if (!env.WORKBOOK_KEY) {
    return Response.json({ error: "WORKBOOK_KEY is not set on the server" }, { status: 500 });
  }
  if (request.headers.get("X-Workbook-Key") !== env.WORKBOOK_KEY) {
    return Response.json({ error: "unauthorized" }, { status: 401 });
  }

  const id = params.id;
  if (typeof id !== "string" || !/^[a-z0-9-]{1,64}$/.test(id)) {
    return Response.json({ error: "bad item id" }, { status: 400 });
  }
  const body = (await request.json().catch(() => null)) as { seen?: unknown } | null;
  if (typeof body?.seen !== "boolean") {
    return Response.json({ error: "seen must be true or false" }, { status: 400 });
  }

  if (body.seen) {
    await env.DB.prepare("INSERT OR REPLACE INTO seen (item_id, seen_at) VALUES (?1, ?2)")
      .bind(id, new Date().toISOString())
      .run();
  } else {
    await env.DB.prepare("DELETE FROM seen WHERE item_id = ?1").bind(id).run();
  }
  return Response.json({ item_id: id, seen: body.seen });
};
```

### `functions/tsconfig.json`

```json
{
  "extends": "../tsconfig.json",
  "compilerOptions": {
    "lib": ["ES2020"],
    "types": ["@cloudflare/workers-types"]
  },
  "include": ["**/*.ts"]
}
```

### `package.json`

```json
{
  "name": "workbook",
  "private": true,
  "version": "0.9.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "dev:full": "npm run build && wrangler pages dev dist",
    "build": "tsc --noEmit && vite build",
    "preview": "vite preview",
    "typecheck": "tsc --noEmit",
    "typecheck:functions": "tsc --noEmit -p functions",
    "lint": "biome check .",
    "lint:fix": "biome check --write .",
    "test": "tsx src/chapters.test.ts && tsx src/library.test.ts && tsx src/notes.test.ts"
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

### `biome.json` (the changed line)

```json
    "includes": ["src/**", "functions/**", "*.ts", "*.json", "!package-lock.json"]
```

### `.dev.vars.example`

```
# Copy this file to .dev.vars (git ignores that name) and put the real key
# after the =. wrangler pages dev reads it; nothing else does.
WORKBOOK_KEY=paste-the-key-here
```

`.dev.vars` is the same file with a real value, and is never in git.

### `src/main.ts`

```ts
// The page's only script. Draws the chapter list and the Library from the
// files in src/, then asks the server which chapters are done and which
// items are seen. This browser remembers one thing: the key that lets you
// write. Everything else is on the server, so every browser agrees.
import { CHAPTERS, chapterId } from "./chapters";
import { isCategory } from "./library";
import libraryData from "./library.json";
import { CHAPTER_IDS, countDone, isStatus, type Status } from "./progress";

const KEY_KEY = "workbook.key";

interface ServerState {
  chapters: Record<string, Status>;
  seen: string[];
}

// GET /api/progress. Null when there is no server (plain npm run dev), so
// the page still draws; every box just shows todo and nothing sticks.
async function loadState(): Promise<ServerState | null> {
  try {
    const res = await fetch("/api/progress");
    if (!res.ok) return null;
    const data = (await res.json()) as { chapters?: unknown; seen?: unknown };
    if (!data.chapters || typeof data.chapters !== "object") return null;
    const chapters: Record<string, Status> = {};
    for (const [id, s] of Object.entries(data.chapters)) {
      if (isStatus(s)) chapters[id] = s;
    }
    const seen = Array.isArray(data.seen) ? data.seen.filter((x) => typeof x === "string") : [];
    return { chapters, seen };
  } catch {
    return null;
  }
}

// Asked once, then kept. Cleared again if the server ever says 401.
function askForKey(): string | null {
  const stored = localStorage.getItem(KEY_KEY);
  if (stored) return stored;
  const typed = window.prompt("Workbook key (William has it):")?.trim() ?? "";
  if (typed) localStorage.setItem(KEY_KEY, typed);
  return typed || null;
}

// PUT with the key in a header. False means nothing changed on the server,
// and the caller puts the checkbox back the way it was.
async function put(path: string, body: unknown): Promise<boolean> {
  const key = askForKey();
  if (!key) return false;
  try {
    const res = await fetch(path, {
      method: "PUT",
      headers: { "Content-Type": "application/json", "X-Workbook-Key": key },
      body: JSON.stringify(body),
    });
    if (res.status === 401) {
      localStorage.removeItem(KEY_KEY);
      window.alert("The server refused that key. Ask William for the current one.");
    }
    return res.ok;
  } catch {
    return false;
  }
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

function renderChapters(list: HTMLOListElement, state: ServerState | null): void {
  const status: Record<string, Status> = {};
  for (const id of CHAPTER_IDS) status[id] = state?.chapters[id] ?? "todo";

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
    box.addEventListener("change", async () => {
      const next: Status = box.checked ? "done" : "todo";
      if (!(await put(`/api/progress/${id}`, { status: next }))) {
        box.checked = !box.checked;
        return;
      }
      status[id] = next;
      li.dataset.status = next;
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

function renderLibrary(state: ServerState | null): void {
  for (const item of libraryData.items) {
    if (!isCategory(item.category)) continue;
    const list = document.querySelector<HTMLUListElement>(`ul[data-category="${item.category}"]`);
    if (!list) continue;
    const li = make("li");
    li.dataset.added = item.added;
    li.dataset.seen = item.seen || state?.seen.includes(item.id) ? "yes" : "no";
    const box = make("input");
    box.type = "checkbox";
    box.title = "seen";
    box.checked = li.dataset.seen === "yes";
    box.addEventListener("change", async () => {
      if (!(await put(`/api/seen/${item.id}`, { seen: box.checked }))) {
        box.checked = !box.checked;
        return;
      }
      li.dataset.seen = box.checked ? "yes" : "no";
    });
    const link = make("a", "", item.title);
    link.href = item.url;
    li.append(box, " ", link, " ", make("span", "why", item.why));
    list.append(li);
  }
}

async function main(): Promise<void> {
  const state = await loadState();
  if (!state) console.warn("No /api/progress here. Run npm run dev:full to talk to the database.");
  const chapterList = document.querySelector<HTMLOListElement>("ol#chapters");
  if (chapterList) renderChapters(chapterList, state);
  if (document.querySelector("ul[data-category]")) renderLibrary(state);
}

main();
```

### `.github/workflows/gates.yml` (the added line, after `npm run typecheck`)

```yaml
      - run: npm run typecheck:functions
```

### Deleted

`src/progress.json`, `src/progress.test.ts`. The `test` script in
`package.json` above no longer lists the second.

### Expected API answers

```
GET  /api/progress                       200 {"chapters":{"00":"done",...,"09":"todo",...},"seen":[]}
PUT  /api/progress/09 (no header)        401 {"error":"unauthorized"}
PUT  /api/progress/09 (wrong key)        401 {"error":"unauthorized"}
PUT  /api/progress/09 {"status":"done"}  200 {"chapter":"09","status":"done","updated_at":"2026-..."}
PUT  /api/progress/09 {"status":"soon"}  400 {"error":"status must be \"todo\" or \"done\""}
PUT  /api/progress/12 {"status":"done"}  404 {"error":"no such chapter"}
PUT  /api/seen/floatnote {"seen":true}   200 {"item_id":"floatnote","seen":true}
PUT  anything, secret unset on server    500 {"error":"WORKBOOK_KEY is not set on the server"}
```

The order of keys inside `chapters` may come out as `"10","11","00",...`:
JavaScript puts number-like keys first. Harmless; the page reads by id.
