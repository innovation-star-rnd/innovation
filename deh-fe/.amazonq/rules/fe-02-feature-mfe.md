# FE-02 · Feature (MFE) rules

Every business area is an independently built micro-frontend under `features/cdx-<name>/src/`.

## Folder contract
```
features/cdx-<name>/src/
  libraries/    # MFE mount shell — webpack ENTRY, UMD export, wires Redux <Provider>
  components/   # feature sections — business logic + data fetching (dispatch/select)
    <section>/  #   sub-folders = atoms/molecules for that section, props only
  organisms/    # reusable UI blocks within the feature — props in, callbacks out, NO Redux
  <x>_state/ or state/  # this MFE's own Redux store, reducers, sagas, actions
  interface/    # TypeScript types
  utils/        # pure helpers (no React, no Redux)
  mock/         # mock data for local/dev only
  constants.ts
```
Flow is always: **RemoteComponent (host) → library → component → organism**.

| Layer | Knows Redux? | Webpack entry? | May import |
|---|---|---|---|
| `libraries/` | only wires Provider/store | **Yes** | components, own store |
| `components/` | **Yes** (useDispatch/useSelector) | No | organisms, own state, utils, interface |
| `organisms/` | No | No | utils, interface, design system/MUI |

## Hard rules
- `organisms/` never import from `components/`, `state`, or `libraries/`. Data via props only.
- No business logic or API calls in `libraries/` — it only mounts.
- No imports **between features** (`features/cdx-a` → `features/cdx-b`). They ship as separate
  bundles. Cross-feature code belongs in `@cdp/common-components` — ask before adding there.
- A feature never imports the host root store (`src/state/store/index.js`) or another MFE's store.
- Don't bundle a second copy of a shared peer (react, react-dom, redux, MUI…) — see FE-03.
- `mock/` data must never be used in production paths unless behind the existing mock flag.

## Add a section to an existing feature
1. `components/<section>/index.tsx` (+ sub-folders for its pieces).
2. Types in `interface/`, constants in `constants.ts`.
3. Actions/reducer/saga in that MFE's existing store (FE-05).
4. Generic UI pieces → `organisms/` (only if reused within the feature).
5. Wire it from the existing library or parent component. No webpack change needed.

## Add a new exposed module (new library entry) to a feature
1. `libraries/<ModuleName>.tsx` exporting the component, wrapped in the feature's Provider/store.
2. Add the entry to `webpack_cdx-<feature>.config.js` (protected — confirm with the developer).
3. Register it so the host can load it (RemoteComponent `compName` / menu/route config —
   **confirm the exact registration point with the developer**, see memory-bank/systemPatterns.md).
4. Run `npm run bundle-<feature>` then `gulp bundle-copy` (or the short alias) and load it in dev.

## Add a brand-new feature
Always ask first — it needs: new `features/cdx-<name>/`, new `webpack_cdx-<name>.config.js`,
new `bundle-<name>` + alias scripts in package.json, version entry, host registration, Jenkins/CDN
pipeline update. Produce the checklist, don't silently create config.
