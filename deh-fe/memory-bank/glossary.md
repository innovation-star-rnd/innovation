# Glossary — CDP frontend

| Term | Meaning |
|---|---|
| CDP | Corporate Digital Platform (this product) |
| Host / shell | The Next.js app in `pages/` that loads MFEs |
| MFE | Micro-frontend = one `features/cdx-*` feature built as UMD bundles |
| cdx-<name> | Feature folder / bundle namespace (`@cdp/cdx-<name>`) |
| library | MFE entry component (webpack entry, mounts store) |
| component | Feature section with Redux + business logic |
| organism | Props-only reusable UI block inside a feature |
| RemoteComponent | Host component that resolves and renders an MFE module |
| requiresConfig | Map that supplies externals (react, redux, MUI…) to loaded bundles |
| compName | Named UMD export picked from a bundle |
| CDN_URL / CDNURL / LOAD_COMPONENT_FROM_CDN | Env vars deciding where bundles load from |
| @cdp/common-components | Shared cross-feature components/utilities (incl. `interceptor()`) |
| @am92/react-design-system | Internal design system (first choice for UI) |
| CDP-Branding | Brand themes, CSS brands, bundle `version-config.json` |
| Maker / Checker | Initiator / approver in dual-control approval workflows |
| Single / Bulk maker | Single-transaction vs file/bulk payment initiation |
| Bene | Beneficiary (payee) |
| BBPS | Bharat Bill Payment System (bill payments) |
| GST / GSTIN | Goods & Services Tax / tax ID; tax e-payment flows |
| ELMS | TODO: expand |
| Payvantage | TODO: describe |
| Handover | SSO hand-off page between systems |
| pprod | Pre-production environment |
| COP-<n> | Ticket ID prefix used in commits |
