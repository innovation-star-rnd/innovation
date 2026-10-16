# System patterns — deh-fe

## 1. Host shell + runtime MFEs
```
Browser ──► Next.js host (pages/)
             │  pages/[...params].tsx  (catch-all)
             ▼
        RemoteComponent.tsx  ── builds URL ──►  /assets/cdx-<m>/bundles/@cdp/cdx-<m>/<ver>/react-component-<Name>.js
             │                                  (or CDN when LOAD_COMPONENT_FROM_CDN === 'true')
             ▼
        remote-module-loader.tsx
             XHR fetch → new Function('require','module','exports', code)
             require → requiresConfig map (host-provided react, react-dom, redux, MUI …)
             export picked by compName → React.lazy + <Suspense>; cached per URL
             ▼
        features/cdx-<m>/src/libraries/<Name>  (mounts Provider + MFE store)
             ▼
        components/<section>  (dispatch/select, business logic)
             ▼
        organisms/<block>     (props-only reusable UI)
```

## 2. MFE build pipeline
```
webpack_cdx-<m>.config.js (merges webpack.config.js base)
  → UMD, library [name], externals = shared peers
  → dist/cdx-<m>/bundles/@cdp/cdx-<m>/<version from CDP-Branding/version/version-config.json>/
gulp bundle-copy  → public/assets/
gulp scss-css     → brand CSS (css-brands)
```

## 3. State
- Host store: `src/state/store/index.js` (portal-level).
- Each MFE: isolated store(s) with own rootReducer, sagaMiddleware, rootSaga, `interceptor()` from
  `@cdp/common-components`. e.g. cdx-payments → `bene_state`, `single_maker_state`, `bulk_maker_state`.
- Async: component dispatch → saga → API → success/failure action → reducer → selector.

## 4. Feature layer contract
| Layer | Redux | Webpack entry | Purpose |
|---|---|---|---|
| libraries/ | wires Provider only | yes | MFE mount shell, UMD export |
| components/ | yes | no | business logic + data fetching |
| organisms/ | no | no | reusable UI inside the feature |

## 5. Security-relevant patterns
- No page caching (`Cache-Control: no-store` on all routes), `poweredByHeader: false`.
- SSO is the only flow with payload decryption (`constants/decrypt.constants.ts`).
- Remote loader executes fetched JS → only trusted, configured base URLs.

## Open points to confirm (update when known)
- Exact registration point for a new module (menuConfig.ts vs route map in `_app.tsx`/catch-all).
- What `pages/api/*` routes are used for.
- Release rule for bumping `version-config.json`.
