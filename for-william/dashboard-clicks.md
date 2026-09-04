# Dashboard clicks

Everything that is not in the repo, with which chapter needs it and the date it
was last confirmed. Cloudflare and GitHub move their menus; when a step does
not match, fix this file with a new date.

## Cloudflare account (before chapter 4)

Last confirmed: not yet (written 2026-09-02 from the plan; confirm on first use).

- Create a **new** Cloudflare account for the project. Not the one that holds
  `floatnote.dev`. Owner login: your alias. Add him as a member with
  Administrator; add yourself as a member from your main login.
- Reason: a teen with an account-wide role on production DNS is the wrong
  risk, and Pages custom domains must be zones in the same account, so the
  domain has to live here too.

## Chapter 4 checkpoint: connect Pages (static, no build)

- Workers & Pages -> Create -> Pages -> Connect to Git.
- Authorize the Cloudflare GitHub App on the `macro-loop-dev` org, restricted to `workbook`.
- Production branch `main`. Framework preset **None**. Build command **empty**.
  Build output directory **`/`**.
- Deploy. Note the `*.pages.dev` URL in `05-merge-and-ship.md`.
- Settings -> Builds -> preview deployments: all branches (so his PR branches
  get preview URLs; useful from chapter 5 on).

## Chapter 6: domain

- Domain Registration -> Register, in the project account, your card,
  auto-renew on, WHOIS privacy on. Registrant: you if he is a minor.
- Workers & Pages -> the project -> Custom domains -> add the apex and `www`.
  Cloudflare creates the CNAMEs itself.
- Rules -> Redirect Rules: `www` -> apex, 301.
- SSL/TLS -> Full (strict).
- Write the renewal date into `06-your-domain.md`.

## Chapter 8: build settings

- Settings -> Builds: build command `npm run build`, output directory `dist`.
- Environment variables (production and preview): `NODE_VERSION` = `22`.
  The repo's `.node-version` should be enough on its own; the variable is
  belt and braces.

## Chapter 9: D1 and the secret

- Storage & Databases -> D1 -> create `workbook` (or let him run
  `npx wrangler d1 create workbook`; either way the id goes in `wrangler.toml`,
  and it is an identifier, not a secret).
- Workers & Pages -> the project -> Settings -> Variables and Secrets:
  `WORKBOOK_KEY`, type **Secret**, production and preview. Generate it with
  `[guid]::NewGuid().ToString("N")` in pwsh; paste; never write it in the repo.
- Once `wrangler.toml` carries `pages_build_output_dir`, bindings come from
  the file and the dashboard shows them read-only. The D1 binding must be in
  the file in the same commit, or the Function deploys without a database.
- Rotating the secret is a checkpoint exercise: change it here, watch his
  saved key stop working, give him the new one.

## Chapter 10: rename, and maybe transfer

- Rename on GitHub (`gh repo rename <domain>` from the repo). Pages follows.
  Old clone URLs redirect; `git remote set-url` is cleaner.
- Transfer to his account: optional, last. It breaks the Pages git connection;
  reconnect in Settings -> Builds -> source.

## Any time: rollback

- Workers & Pages -> the project -> Deployments -> the previous good one ->
  Rollback to this deployment. Show him this once in chapter 5.
