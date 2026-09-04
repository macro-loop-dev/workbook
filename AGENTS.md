# Rules for AI assistants in this repo

This repo is a coding workbook. The person asking you for changes is learning,
and the point of most tasks is what they see in the diff, not the result. These
rules exist so the diff stays readable and the lesson stays theirs.

1. **Explain every change in one sentence before making it.** They are
   deciding whether to accept it; the sentence is what they decide on.
2. **Touch only the files the request names.** If the change needs another
   file, say which and why, and stop. Do not "also fix" anything.
3. **"Mark chapter N done" or "mark <item> seen" is exactly one edit**: the
   `data-status` attribute on that chapter's `<li>` in `index.html`, or the
   `data-seen` attribute on that item's `<li>` in `library/index.html`. From
   chapter 8 it is one key in `src/progress.json` or `src/library.json`; from
   chapter 9 it is nothing in this repo, because progress lives in a database.
   Never change anything else in the same edit.
4. **A new note** means: copy `notes/_template.html` to a new file, fill it in,
   add one line to the Pages list in `notes/index.html`. Never edit another
   note. Never edit `notes/til.html` except to add an entry at the top.
5. **Ask before installing anything** (`npm install`, `winget`, extensions)
   and before any command that changes git state (`commit`, `push`, `reset`,
   `switch`, `checkout`). Reading git state (`status`, `diff`, `log`) is fine.
6. **Never read from or write to `for-william/`.** Those are the reviewer's
   notes. If asked what they say, decline and say why.
7. **Never put a key, token or password in any file.** If you see one, say so
   and stop.
8. **Plain HTML, CSS and JavaScript.** No framework, no CSS library, no
   bundler, no TypeScript, until the chapter being worked on says so. Every
   colour comes from `styles.css` tokens; no raw hex, no `style=` attributes.
9. **The terminal is PowerShell 7 on Windows.** Chain commands with `;`, not
   `&&`. Never suggest `sudo`, `bash`, `apt`, `brew` or `curl | sh`.
10. **When a command fails, ask for the exact error text** and diagnose that.
    Do not guess a fix that installs something.

Files that exist because of a specific chapter (`script.js`, `package.json`,
`src/`, `functions/`) must not appear before that chapter. If you are asked to
create one early, say which chapter introduces it.
