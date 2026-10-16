# Feature catalog — deh-fe

`features/` folders seen: cdx-account-manager, cdx-assets, cdx-bbps, cdx-commercial-cards,
cdx-dashboard, cdx-deposit, cdx-drafts, cdx-elms, cdx-global, … (complete this list).

| Bundle script | Webpack config | Short alias (bundle + copy) |
|---|---|---|
| bundle-payments | webpack_cdx-payments.config.js | `payments`, `bp` (+dev), `bundle-pay` |
| bundle-account-manager | webpack_cdx-account-manager.config.js | `account-manager` |
| bundle-reports | webpack_cdx-reports.config.js | `bundle-rep` |
| bundle-drafts | webpack_cdx-drafts.config.js | `drafts`, `b-drafts` |
| bundle-elms | webpack_cdx-elms.config.js | `elms` |
| bundle-service | webpack_cdx-service-request.config.js | `bundle-serv` |
| bundle-gst | webpack_cdx-gst.config.js | `gst` |
| bundle-tax-epayment | webpack_cdx-tax-epayment.config.js | `epayment` |
| bundle-prelogin | webpack_cdx-pre-login.config.js | `bpl` |
| bundle-dashboard | webpack_cdx-dashboard.config.js | `bds` |
| bundle-commercial-cards | webpack_cdx-commercial-cards.config.js | — |
| bundle-treasury | webpack_cdx-treasury.config.js | `bt` |
| bundle-shoppingmall | webpack_cdx-shopping-mall.config.js | `spm` |
| bundle-deposits | webpack_cdx-deposit.config.js | `bd` |
| bundle-graceful-error | webpack_cdx-graceful-error.config.js | `errors` |
| bundle-payvantage | webpack_cdx-payvantage.config.js | — |
| bundle-bbps | webpack_cdx-bbps.config.js | `bbps` |
| (release) b-rel | webpack_commercial_cards_rel.config + webpack_service_rel.config | `b-rel`, `b-serv` |

Combined: `b` (several bundles), `b-prod` (`--mode production`), `b-dev` (`--devtool inline-source-map`).

Keep this table in sync when adding a feature or script.
