# Decisions & tech debt — deh-fe

## Standard choices for NEW code
| Area | Decision | Status |
|---|---|---|
| Dates | moment (+ moment-timezone) | Agreed |
| Async side effects | redux-saga in the MFE's own store | Matches all existing stores |
| Reducer style | follow the target store's existing style | Agreed |
| UI | @am92/react-design-system → MUI v5 | Existing standard |
| Drag & drop | @dnd-kit | Recommended — confirm |
| Router | Pages Router only | Agreed |
| Encryption | SSO flow only | Agreed |
| Tests | jest.config.ts + Testing Library, jsdom docblock for components | Recommended |

## Known tech debt (don't extend; fix only when asked)
- Five date libraries installed (moment, moment-timezone, dayjs, date-fns, react-dates).
- `react-beautiful-dnd` alongside `@dnd-kit` (rbd is archived upstream).
- `"clsx": "latest"` — unpinned version.
- Two Jest configs (package.json `jest` block vs `jest.config.ts`); `testEnvironment: node`.
- `constants_notused/` folder kept in repo.
- Asset rules duplicated for Turbopack (dev) and webpack (build).
- Root `/` redirect is permanent (308) but env-dependent.
- Windows-only `reinstall` scripts.
- Remote loader uses `new Function` eval (needs trusted origins; blocks a strict CSP).
- Crypto polyfills in devDependencies (browserify-cipher, pbkdf2, …) — only SSO needs crypto.
