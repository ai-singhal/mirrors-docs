# Mirrors docs (Mintlify)

The customer-facing documentation site, built with [Mintlify](https://mintlify.com/docs).
Content is `.mdx` pages; structure, branding, and navigation live in `docs.json`.

## Develop locally

```bash
cd docs
npx mint dev          # serves http://localhost:3000 with live reload
npx mint broken-links # validate internal links before pushing
```

## Deploy

Mintlify deploys from the **public mirror repo**
[`ai-singhal/mirrors-docs`](https://github.com/ai-singhal/mirrors-docs), because
its GitHub connection can't see this private monorepo. The `docs-sync` workflow
(`.github/workflows/docs-sync.yml`) force-pushes this directory there on every
push to `main` that touches `docs/**`. **This directory is the source of truth;
never edit mirrors-docs directly** (the next sync overwrites it).

One-time setup (verified against mintlify.com/docs):

1. **Sign into github.com as `ai-singhal`** (the repo owner) before anything:
   the GitHub App can only be installed by the owning account.
2. Onboard at [mintlify.com/start](https://mintlify.com/start). Either connect
   GitHub there ("Add GitHub repo" → `ai-singhal/mirrors-docs`), or skip Git
   entirely and wire it after: dashboard →
   [Git Settings](https://app.mintlify.com/settings/deployment/git-settings) →
   setup wizard → manual setup → authorize GitHub → pick
   `ai-singhal/mirrors-docs`, branch `main`. Leave "docs.json is in a
   subdirectory" OFF (it's at the repo root). Install the GitHub App when
   prompted (or at [GitHub App settings](https://app.mintlify.com/settings/organization/github-app)),
   scoped to just `mirrors-docs`.
3. The site goes live at `https://<project>.mintlify.site` (shown on the
   dashboard Overview page). LIVE: https://mirrors.mintlify.site
4. **Subpath hosting at `www.runmirrors.com/docs`** (chosen over a
   `docs.` subdomain so the docs stay on the main domain): dashboard →
   [Custom domain setup](https://app.mintlify.com/settings/deployment/custom-domain)
   → enable the **Host at** toggle → base path `docs` → domain
   `www.runmirrors.com` → Add domain. **No DNS records needed**: Vercel keeps
   serving the domain and `frontend/vercel.json` proxies `/docs`, `/_mintlify`,
   `/mintlify-assets`, and `/api/request` to `mirrors.mintlify.site` via
   rewrites (the strict CSP header is scoped to exclude those proxied paths;
   Mintlify serves its own headers there). Mintlify then rebuilds the site to
   serve under `/docs`.

If the GitHub connect misbehaves, Mintlify's documented reset: uninstall the app
at github.com/settings/installations AND revoke it at
github.com/settings/apps/authorizations, then reinstall from Git Settings and
re-authorize under [My Profile](https://app.mintlify.com/settings/account).

The frontend links to the docs via `DOCS_URL` in `frontend/src/lib/docs.ts`.
Flip it to `https://www.runmirrors.com/docs` (and the Docs line in
`frontend/public/llms.txt`) once the subpath is live end-to-end.

## Notes

- Mintlify auto-hosts an MCP server for the docs at `https://docs.runmirrors.com/mcp`
  (search/retrieval over this content, separate from the product MCP server at
  `api.runmirrors.com/mcp`). Nothing to configure; only pages in `docs.json`
  navigation are indexed.
- Brand: primary `#0C82CF` (matches `frontend/src/theme.ts`), Geist, and the
  shared `logo.png` / `favicon.png` copied from `frontend/public/`.
- Keep the MCP endpoint written **without a trailing slash** everywhere
  (`https://api.runmirrors.com/mcp`). See `frontend/src/components/mcp/clients.ts`.
