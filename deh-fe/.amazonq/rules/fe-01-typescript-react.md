# FE-01 · TypeScript & React coding rules

## TypeScript (5.8)
- New code is `.ts`/`.tsx`. Write as if `strict` were on, even where the file isn't.
- No `any`. Use precise types, generics, or `unknown` + narrowing. No `// @ts-ignore`;
  if unavoidable use `// @ts-expect-error <reason>`.
- Props and API shapes are `interface`s in the feature's `interface/` folder
  (`XxxProps`, `XxxRequest`, `XxxResponse`). Don't redeclare a type that already exists there.
- Prefer union types / `as const` objects over `enum`.
- Exported functions and hooks declare return types. Avoid non-null `!` — handle null explicitly.
- Use optional chaining / nullish coalescing (`?.`, `??`) instead of `&&` chains and `||` defaults
  (`||` breaks on `0` and `''`, which matter for amounts).

## React 19
- Function components and hooks only. No class components, string refs, legacy context.
- React 19 removed `defaultProps` for function components → use default parameter values.
- `ref` can be passed as a normal prop in React 19; don't add `forwardRef` to new components
  unless a library (e.g. MUI slot) requires it.
- Rules of hooks; complete `useEffect` dependency arrays; always clean up timers/listeners/
  subscriptions. Don't use `useEffect` to compute derived state — compute during render.
- List keys must be stable IDs, never the array index for editable/reorderable lists.
- `useMemo`/`useCallback` only where it matters: AG Grid `columnDefs`/`defaultColDef`, props of
  heavy children, values used in effect deps.
- Pages Router renders on the server too: guard `window`, `document`, `localStorage`
  (use inside `useEffect` or check `typeof window !== 'undefined'`).

## UI
- Order of preference: `@am92/react-design-system` → MUI v5 (`@mui/material` 5.15) → custom.
  Use MUI **v5** APIs only (not v6/v7 docs). Don't add another UI kit.
- Styling: follow the file's existing approach (Emotion `styled`/`sx`, or SCSS from `styles/`).
  No inline style objects for anything reused. Brand colours/themes come from CDP-Branding.
- Grids: AG Grid (enterprise) for data-heavy tables; MUI X DataGrid only where the feature already
  uses it. Charts: AG Charts.
- Every interactive element is keyboard-accessible and labelled (`aria-label` for icon buttons).

## Approved libraries (don't introduce alternatives)
| Need | Use | Don't use in new code |
|---|---|---|
| Dates/time | `moment`, `moment-timezone` | `dayjs`, direct `date-fns`, `react-dates` |
| HTTP | the feature's axios instance with `interceptor()` from `@cdp/common-components`, called from sagas | raw `fetch`, axios inside components |
| Utilities | `lodash` per-method import (`import get from 'lodash/get'`) | whole-lodash import |
| Drag & drop | `@dnd-kit/*` | `react-beautiful-dnd` (archived upstream) |
| HTML sanitising | `dompurify` | unsanitised `dangerouslySetInnerHTML` |
| Excel/PDF | `exceljs`, `read-excel-file`, `jspdf`(+autotable), `react-pdf` | new export libs |

## Security (banking app)
- Never render HTML from APIs/users without `DOMPurify.sanitize`.
- Never log PII or secrets (`console.log` of account numbers, PAN, GSTIN payloads, OTP, tokens).
  Remove debug logs before commit.
- Mask account/card numbers in UI unless the screen's requirement says otherwise.
- `NEXT*` env values are delivered to the browser — never put secrets in them.
- Don't add new storage of tokens/PII in `localStorage`/`sessionStorage`; follow existing helpers.
- Encryption/decryption exists only for the SSO flow — don't add crypto elsewhere.
- Monetary values: never do float arithmetic for display totals that must be exact; format with
  the existing utils.

## Naming & files
- Folders kebab-case (`approvals-due/`, `confirmation-dialog/`); component entry `index.tsx`.
- Components PascalCase; hooks `useXxx.ts`; constants `UPPER_SNAKE_CASE` in `constants.ts`.
- One component per file; keep files < ~300 lines — split into sub-folders (atoms/molecules).

## Testing (Jest 30 + Testing Library)
- `npm test` uses `jest.config.ts` (the package.json `jest` block is legacy).
- Test files `*.spec.ts(x)`. Component tests need DOM: add `/** @jest-environment jsdom */`
  at the top if the config's environment is `node`.
- Test behaviour via Testing Library queries (`getByRole` first), not implementation details.
- Sagas: test with the generator step pattern or `redux-saga-test-plan` style already used; mock API.
