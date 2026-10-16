# FE-04 · Pages & routing rules

## Router
- **Pages Router only.** Do not create `app/`, server components, server actions or `'use client'`.
- `pages/` is the **host shell**. Business screens are MFE modules, not pages.

## Existing pages
| Page | Role |
|---|---|
| `_app.tsx` | Shell bootstrap: runtime config init, merged URL config, route setup (protected) |
| `_document.tsx` | HTML document (protected) |
| `index.tsx` | Root; `/` redirects per `LOGIN_REVAMP_JOURNEY` to landing page / `/pre-login` |
| `[...params].tsx` | Catch-all: resolves the route to a feature module rendered via `RemoteComponent` |
| `shopping-mall/index.tsx`, `shopping-mall/[...params].tsx` | Shopping-mall area routing |
| `pre-login-authenticate/`, `sso/`, `handover/`, `userOtp/`, `logout/` | Auth/SSO flow (protected) |
| `404/`, `forbidden/` | Error screens |
| `api/` | Next API routes |

## Rules
- A new business screen = a new section/module inside a feature (FE-02), reached through the
  catch-all + menu/route config — **not** a new file in `pages/`. Confirm the route/menu
  registration (`constants/menuConfig.ts`) with the developer before editing it.
- Create a new page only for shell-level concerns (auth, error, handover). Ask first.
- Don't add `getServerSideProps`/`getStaticProps`/`getInitialProps` without approval — feature data
  is fetched client-side through sagas.
- Use `next/router` (`useRouter`) for navigation; `next/link` for links. Don't use `window.location`
  except where the SSO/logout flow already does.
- Read route params defensively (`router.isReady`, params may be `string | string[]`).
- SSO is the only place with encryption/decryption (`constants/decrypt.constants.ts`). Don't copy
  that logic into other pages.
- Don't touch the root redirect or `headers()` in `next.config.js` without approval (FE-03).
