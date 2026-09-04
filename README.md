# The Workbook

A coding workbook that is also the website being built. You learn to work on a
real project by changing this one, and the way you are allowed to change it
gets more real every chapter.

## Open it

Double-click `index.html`. That is the whole site until chapter 8. Chapter 0
tells you how to get the tools and clone this repo; start there.

## The ladder

| Ch | You mark a chapter done by | You learn |
|---|---|---|
| 0 | watching William do it | what the machine needs |
| 1 | editing one attribute in `index.html` and refreshing | the file is the site |
| 2 | asking Cursor's AI to do the same edit, and reading the diff | the diff is where you decide |
| 3 | committing that edit in the terminal | git is a save button with a reason field |
| 4 | pushing it on a branch and opening a pull request | a remote is a backup and a conversation |
| 5 | merging, and watching the live site change | `main` is the live site |
| 6 | the same, at your own domain | names are a lookup table |
| 7 | ticking a checkbox that JavaScript remembers | state, and where it lives |
| 8 | changing one key in a JSON file, with tests and CI green | tools that check before a human does |
| 9 | ticking on the live site, saved by a server you wrote | content is versioned, state is live |
| 10 | turning the site into your portfolio | moving code without breaking it |
| 11 | a real pull request to FloatNote | someone else's repo, someone else's rules |

Alongside: the **Library** (`library/`) is what William shares with you, each
item with one line on why, arriving as pull requests you review. The
**Notes** (`notes/`) are yours.

## Rules for the AI

`AGENTS.md`. Cursor reads it on its own. The short version: one sentence before
every change, touch only what was asked, "mark it done" is one attribute, ask
before installing anything, never touch `for-william/`.

## Deliberately absent

No framework, no CSS library, no bundler until chapter 8, no database until
chapter 9. No Docker, no password manager, no VPN. No CI job before there is
something a human cannot see: the only workflow today is the secret scanner.
Every line on screen has a file you can point at.
