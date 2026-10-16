# FE-03 · Bundling & build rules

There are **two build systems**. Know which one your change affects.

## 1. Host shell — Next.js (`next.config.js`)
- `next dev` runs **Turbopack**; `next build` runs **webpack**. Any loader/asset rule must be
  added in **both** places: `turbopack.rules` and the `webpack()` function. Verify with
  `npm run dev` **and** `npm run build`.
- Do not change without approval (each exists for a reason):
  - React 19 `resolve.alias` for `react`, `react-dom`, `react/jsx-runtime`, `react/jsx-dev-runtime`
    (fixes duplicate-React prod issues).
  - `resolve.alias.canvas = false` (needed by react-pdf).
  - Timestamped `filename`/`chunkFilename` (cache-busting).
  - `generateBuildId` (git-hash placeholder), `poweredByHeader: false`.
  - Global `Cache-Control: no-store` headers (banking: no caching of pages).
  - `images.loader: 'imgix'` + `disableStaticImages: true` → `import x from './a.png'` goes through
    `file-loader`, not Next's image pipeline.
- `transpilePackages` — add a package here only if it ships untranspiled ESM and breaks the build.
- `/` redirect depends on `LOGIN_REVAMP_JOURNEY` and is `permanent: true` (308, cached by browsers).
  Changing the destination won't reach users who already cached it — flag this if touched.

## 2. Feature MFEs — standalone webpack
- `webpack_cdx-<feature>.config.js` merges the base `webpack.config.js` and emits **UMD** bundles:
  `dist/cdx-<feature>/bundles/@cdp/cdx-<feature>/<version>/react-component-<Name>.js`
  (`libraryTarget: 'umd'`, `library: '[name]'`).
- `<version>` comes from `CDP-Branding/version/version-config.json`. The URL contains the version,
  so a changed bundle normally needs a version bump to reach users (confirm release process).
- `gulp bundle-copy` copies `dist/**` → `public/assets/`; `gulp scss-css` (`css-brands`) builds
  brand CSS. Both are generated — never hand-edit `dist/` or `public/assets/` bundles.

## Externals contract (most common source of runtime breakage)
- Peers (`react`, `react-dom`, `redux`, `@mui/material`, …) are **externals**: not bundled, supplied
  by the host at runtime through the `requiresConfig` map in `remote-module-loader.tsx`.
- Adding a library to an MFE:
  - **Bundled** (default for small, feature-only libs): just import it; watch bundle size.
  - **External**: must be added to the externals list **and** to `requiresConfig` **and** be a
    host dependency. Ask before doing this.
- Upgrading React / MUI / redux affects host and every MFE together — never upgrade in one place.

## Runtime loading (don't change casually)
- `RemoteComponent.tsx` builds the URL: `CDN_URL + "assets/cdx-<m>/bundles/@cdp/cdx-<m>/" + version + file`,
  or `CDNURL + "@cdp/cdx-<m>/" + version + file` when `LOAD_COMPONENT_FROM_CDN === 'true'`.
- `remote-module-loader.tsx` fetches via XHR and evaluates with `new Function('require','module','exports', code)`,
  picks the export by `compName`, wraps in `React.lazy` + `Suspense`, caches per URL.
- Security: the loader executes fetched code. Only load from configured CDN/same-origin bases;
  never build a bundle URL from user input or query params.
- Wrap remote modules in an error boundary so a failed bundle doesn't blank the shell.

## Scripts
- `bundle-<feature>` → `webpack --config webpack_cdx-<feature>.config.js`
- Short aliases bundle + copy (+css): `bds` dashboard, `bp` payments, `bpl` prelogin, `bt` treasury,
  `spm` shopping mall, `bd` deposits, `gst`, `epayment`, `bbps`, `elms`, `drafts`, `errors`, … (see featureCatalog)
- Env start scripts: `dev-start`, `qa-start`, `uat-start`, `pprod-start`, `prod-start` use
  `react-env --prefix NEXT --path .env.<env>` (runtime env injection, port 5000).
- `reinstall*` scripts are Windows-only (`rmdir /s /q`, `del /f /q`).

## Dependencies
- Pin exact versions (no `^`, no `latest`). Security pins live in `overrides` — don't remove them.
- Prefer the existing library for a need (see FE-01 table). Ask before adding anything.
