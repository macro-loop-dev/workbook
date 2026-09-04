# Chapter 1: reviewer notes

## What to check

- Counter reads 2 of 12 (ch 0 yours, ch 1 his).
- `--accent` is back to `#2733c9`. If it is not, that is fine, but ask him
  where the colour lives and why one line changed everything.
- One Library item is `data-seen="yes"`; one note page exists and is linked
  from `notes/index.html`; TIL has an entry at the top and the example is gone.

## What to ask

"What is `data-status`?" Good answers mention: it is a label on the list item;
CSS reads it; nothing else does. Then: "what would happen if you typed
`data-status="finished"`?" (nothing counts it; the value is part of the
contract.)

## What you do

Open Cursor's terminal (Ctrl+`), type `git status`, say nothing. He will see
`M index.html`, `M styles.css`, `?? notes/2026-...html`. If he asks, "two
chapters from now."

## Answer key

The one-attribute diff for this chapter:

```
-    <li data-chapter="01" data-status="todo">
+    <li data-chapter="01" data-status="done">
```
