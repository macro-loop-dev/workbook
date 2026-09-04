# Chapter 6: reviewer notes

Domain: `________________`
Registered: `2026-__-__` Renews: `2027-__-__` (write both in at the
checkpoint; auto-renew on; your card on file in the project account.)

## Before the session

- He has picked a name. Check it is free and that the ending is one Cloudflare
  Registrar sells (`.com`, `.dev`, `.me`, `.net` and most common ones are;
  some country endings are not). Have a second choice ready.
- Register in the **project** Cloudflare account, not the one holding
  `floatnote.dev` (`dashboard-clicks.md`). The zone and the Pages project
  must be in the same account or Custom domains will not attach.
- Registrant: you, if he is a minor. Your card. WHOIS privacy is on by
  default at Cloudflare; confirm it. The registrant-verification email goes
  to the registrant address: click it within fifteen days or the name is
  suspended.
- Know that a `.dev` name is HTTPS-only in every browser (HSTS preload). It is
  a fine choice; just expect the `http://` question.

## What to check

- DNS -> Records: exactly two proxied CNAMEs, apex and `www`, both pointing
  at `<project>.pages.dev`. No stray rows.
- Custom domains: both Active. SSL/TLS: Full (strict). Rules -> Redirect
  Rules: the WWW-to-root template, 301, enabled.
- In pwsh: `Resolve-DnsName <domain>` and `Resolve-DnsName www.<domain>` both
  answer with two Cloudflare A records; `Resolve-DnsName <domain> -Type NS`
  lists two `*.ns.cloudflare.com` names.
- `https://<domain>` shows the padlock and 6 of 12 before his PR, 7 of 12
  after. `www.` lands on the apex. The `.pages.dev` address still works.
- His PR description contains the live URL. That is the mechanic for this
  chapter and every chapter after.

## What to ask

Have him read the DNS table aloud, one row at a time: type, name, content,
and what it is for. Good answers say "the answer for this name is whatever
the answer for that name is" for CNAME, and "Cloudflare answers with its
own addresses and passes visitors through" for the orange cloud.

"Why two rows?" (the bare name and `www` are different names; DNS does not
know they are related.) "What stops working if you delete the `www` one?"
(`www.` only; the apex is untouched.) "What did we buy?" (a year of the right
to say where the name points; renewal is what keeps it.)

"In the story, what else lived in FloatNote's table?" (email rows; the table
serves more than the website.)

## What you do

- Register on your screen with him watching. Say what each setting is for as
  you click it.
- Attach both custom domains together: he clicks, you watch. Then let him
  read the two rows Cloudflare wrote.
- SSL/TLS -> Full (strict). Rules -> Redirect Rules -> WWW to root.
- Write the registration and renewal dates at the top of this file. Put a
  reminder in your own calendar for a month before renewal, in case the card
  has expired by then.
- Review his PR: one attribute, and the description must carry the URL. If
  the URL is missing, request changes; it is the first time you do, and it is
  a cheap one to learn on.
- Out of sight: nothing new this chapter.

## Common mistakes

- **Registered under the wrong account.** Notice before any rows exist; a
  zone move between accounts is a chore.
- **"DNS name does not exist" long after the records are there, on one
  device.** Negative caching. `ipconfig /flushdns` on the laptop; on the phone,
  toggle Wi-Fi off to use mobile data. `Resolve-DnsName <domain> -Server
  1.1.1.1` asks Cloudflare directly and skips every cache in between.
- **Only one of apex/`www` added.** Each is its own custom domain and its own
  row.
- **Certificate pending past fifteen minutes.** Check the row is proxied;
  remove and re-add the custom domain.
- **He clicks through a certificate warning.** Stop him. It is a habit that
  will hurt on someone else's site.
- **The verification email is ignored.** It goes to you. Check spam on the
  day.
- **`http://` on a `.dev` name.** By design. `https://` or nothing.

## Answer key

The one-attribute diff:

```
-    <li data-chapter="06" data-status="todo">
+    <li data-chapter="06" data-status="done">
```

Commit message: `docs: mark chapter 6 done`. Branch: `chapter-6`. PR:

```
gh pr create --title "docs: mark chapter 6 done" --body "Live at https://<domain>"
```

The DNS table when it is right (Content is the project's `.pages.dev`
hostname, not the full URL):

| Type  | Name       | Content                 | Proxy   | TTL  |
|-------|------------|-------------------------|---------|------|
| CNAME | `<domain>` | `<project>.pages.dev`   | Proxied | Auto |
| CNAME | `www`      | `<project>.pages.dev`   | Proxied | Auto |

Shape of `Resolve-DnsName <domain>` when it is right (addresses vary):

```
Name        Type  TTL  Section  IPAddress
----        ----  ---  -------  ---------
<domain>    A     300  Answer   104.21.x.x
<domain>    A     300  Answer   172.67.x.x
```
