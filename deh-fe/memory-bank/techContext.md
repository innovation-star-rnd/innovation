# Tech context — deh-fe

## Versions (from package.json)
| Area | Package | Version |
|---|---|---|
| Framework | next | 16.3.8 (Pages Router) |
| UI runtime | react / react-dom | 19.2.3 |
| Language | typescript | 5.8.3 |
| State | redux / react-redux / @reduxjs/toolkit / redux-saga | 5.0.1 / 9.2.0 / 2.11.2 / 1.2.3 |
| Design system | @am92/react-design-system | 2.9.12 |
| UI kit | @mui/material, @mui/system, @mui/icons-material | 5.15.x |
| Grids/charts | ag-grid-* 31.3.4 (enterprise), ag-charts-* 11.0.3, @mui/x-data-grid 7.4.0 |
| Styling | @emotion/*, sass 1.92, CSS via gulp-sass |
| HTTP | axios 1.18.1 |
| Dates | moment 2.29.4, moment-timezone 0.5.43 (standard) |
| Bundling | webpack 5.105.2, webpack-cli 6, babel, @svgr/webpack, file-loader |
| Tasks | gulp 5 (bundle-copy, scss-css, bundle-module) |
| Tests | jest 30, ts-jest, @swc/jest, @testing-library/react 16, jest-junit |
| Quality | eslint-config-next 16.3.8, eslint-config-prettier, husky 7 |

## Build/run
- `next dev` → Turbopack (port 5000) · `next build` → webpack.
- MFE bundles: `webpack --config webpack_cdx-<feature>.config.js` → `dist/` → `gulp bundle-copy` → `public/assets/`.
- Env: `react-env --prefix NEXT --path .env.<dev|qa|uat|pprod|prod>` (runtime injection).
- `preinstall`: `npm-force-resolutions`; `overrides` hold security pins.
- `package.json` `proxy`: `http://localhost:8080/`.

## Repo layout (top level)
`pages/`, `features/`, `src/` (host state etc.), `constants/`, `config/`, `CDP-Branding/`,
`public/{assets,images,json}`, `docs/`, `__mocks__/`, `test/`, `Jenkins/`, `Kubernetes/`, `nginx/`,
`dist/` (generated), `constants_notused/` (dead — ignore).

## Deployment
Jenkins pipelines, Docker/Kubernetes manifests (AWS EKS), nginx config — folders in repo root.
MFE bundles served from `public/assets` or a CDN (`LOAD_COMPONENT_FROM_CDN`).
