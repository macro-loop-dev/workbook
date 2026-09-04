# Chapter 2: reviewer notes

## What to check

- `index.html` has one new paragraph directly under the `<h1>` (or under the
  lede; either is fine) and nothing else new. From the terminal:
  `git diff index.html` should show exactly four changed lines: the three
  `data-status` lines for 00, 01, 02 and the paragraph. Anything else means he
  accepted something he did not ask for.
- `styles.css` is untouched. `git status` must not list it. If it does, run
  `git diff styles.css` with him and ask what he would have had to review.
- A new `notes/2026-MM-DD-what-a-diff-is.html` exists, copied from the
  template, and `notes/index.html` has one new line under Pages (or the
  commented example on line 39 uncommented and edited). Two files, no more.
- His paper note or screenshot from the "make the site look modern" ask
  exists and names at least one file and one unrequested change.
- Counter reads 3 of 12. TIL has a new entry at the top.
- Manual verification the scaffold could not do: Cursor Settings -> Rules
  lists `workbook.mdc` as always applied. Look yourself.

## What to ask

"What did it change that you did not ask for?" Keep asking until the answer is
a file and a line, not "the colours". Then: "If the explanation says it changed
one line and the diff shows three, which one is right?" (the diff; the
explanation is a claim). Then have him say the rule: review the diff, not the
explanation.

Also: "What would you have done if the note task had touched three files?"
(reject, ask why; rule 2 says it should have stopped and named the file.)

## What you do

- Ask the assistant, in front of him, to mark a Library item seen
  (`Mark the Cursor docs item seen`). Read the diff aloud, then Reject it,
  so he sees a reject that is not about a bad diff: rejecting is free.
- Open the terminal, `git status`, say nothing. He will see two `M` lines
  more than last time and two `??` files. If he asks: "next chapter".

## Common mistakes

- Cursor opened at `~\dev` or `~` instead of `~\dev\workbook`; rules do not
  load. Fix: `cursor ~\dev\workbook`.
- He used Ask mode for an edit. Ask mode prints code in the chat and changes
  nothing; he may have pasted it in by hand. Not wrong, but the diff never
  appeared, so he never reviewed it. Show `git diff`.
- He accepted the "modern" diff. Ask him to list what changed, then
  `git restore styles.css` together, and say what that command did.
- He wrote the paragraph he wanted but left the assistant's version in too.
  Fine; it is his file.
- Free-tier cap reached mid-chapter. The remaining asks (the note page, mark
  done) can be done by hand this once; the mechanic still counts if the
  one-attribute diff was seen at least once.

## Answer key

The chapter-2 diff for `index.html`, before the mechanic (paragraph wording
will differ; position under `<h1>` or under the lede both acceptable):

```
   <h1>The Workbook</h1>
+  <p>This workbook is for someone who has never written code and has a Windows
+  laptop, a browser and an afternoon a week.</p>
   <p class="lede">This page is the workbook and the scoreboard. It is a website
```

The mechanic, exactly one attribute:

```
-    <li data-chapter="02" data-status="todo"><span class="num">2</span><a href="chapters/02-ask-the-ai.html">Ask the AI, review the diff</a><span class="meta"><span class="badge">ask the AI</span><span>1-2 h</span></span></li>
+    <li data-chapter="02" data-status="done"><span class="num">2</span><a href="chapters/02-ask-the-ai.html">Ask the AI, review the diff</a><span class="meta"><span class="badge">ask the AI</span><span>1-2 h</span></span></li>
```

The note task, two files. New file `notes/2026-MM-DD-what-a-diff-is.html` is
`notes/_template.html` with `TITLE` replaced twice by `What a diff is` and
`DATE` by the date, headings kept. The line in `notes/index.html`, inside
`<ul class="notes-list" id="pages">`:

```
    <li><span class="date">2026-MM-DD</span><a href="2026-MM-DD-what-a-diff-is.html">What a diff is</a></li>
```

If it uncommented line 39 and changed the date instead, that is the same
result.

The explain ask should name these two rules from `styles.css`:

```
.chapters li[data-status="done"] { counter-increment: done; }
.progress::after {
  content: counter(done) " of 12 done";
  ...
}
```
