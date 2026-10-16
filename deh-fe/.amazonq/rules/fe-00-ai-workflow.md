# FE-00 · How the AI must work in deh-fe

Applies to every task in this repo. Higher-numbered rule files add detail; if they conflict, ask.

## Before writing code
1. Identify **where** the change lives: shell (`pages/`, `src/`, `constants/`) or a feature MFE
   (`features/cdx-<name>/src/...`), and which layer (`libraries/`, `components/`, `organisms/`, state).
2. Find the closest existing implementation in the **same feature** and mirror its structure,
   naming, imports and state pattern. Consistency with the feature beats personal preference.
3. For changes touching more than 2 files, first list the files you will create/modify and why.
4. If a requirement, endpoint, env var, action name or response shape is not visible in the code,
   **ask** — never invent it.

## While writing code
- Make the smallest change that solves the task. Do not reformat, rename or reorder unrelated code.
- Do not add an npm dependency without asking. Check `memory-bank/decisions-and-tech-debt.md`
  for the approved library in each category first.
- Never edit generated output: `dist/`, `public/assets/` bundles, `.next/`, `coverage/`.
- Ignore `constants_notused/` — never import from it.

## Protected files — ask before editing
`next.config.js`, `webpack.config.js`, `webpack_cdx-*.config.js`, `gulpfile.js`,
`RemoteComponent.tsx`, `remote-module-loader.tsx`, `CDP-Branding/version/version-config.json`,
`pages/_app.tsx`, `pages/_document.tsx`, `pages/[...params].tsx`, `src/state/store/index.js`,
`constants/decrypt.constants.ts`, `constants/menuConfig.ts`, SSO pages (`sso`, `handover`,
`pre-login-authenticate`, `userOtp`, `logout`), `package.json` (dependencies/overrides/scripts),
`.env*`, `Jenkins/`, `Kubernetes/`, `nginx/`, `.husky/`.

## After writing code
- Run (or tell the developer to run) `npm run lint` and the relevant tests; for bundle/config
  changes also `npm run build` and the affected `bundle-*` script.
- Summarise: files changed, layer of each file, anything you could not verify.

## Commits
Follow the existing format: `COP-<ticket> FEAT: <summary>` (use FIX/CHORE etc. as the team does).
