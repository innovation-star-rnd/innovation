# FE-05 · State management (Redux + redux-saga)

## Store topology
- `src/state/store/index.js` — **host/portal store** for shell-level state only.
- Each MFE has its **own isolated store**: own `rootReducer`, own `sagaMiddleware` + `rootSaga`,
  and calls `interceptor()` from `@cdp/common-components`.
  Example: `features/cdx-payments/src/bene_state/beneStore.ts`,
  `single_maker_state/makerStore.ts`, `bulk_maker_state/makerStore.ts`.
- One feature can have several stores (one per flow). Add new state to the store of the flow you
  are working in; create a new store only with approval.
- Never import or dispatch to another MFE's store or the host store from a feature.

## Data flow (standard)
`component` → `dispatch(getXxx(payload))` → saga (`takeLatest`) → API call via the feature's
axios/interceptor → `put(getXxxSuccess(data))` / `put(getXxxFailure(error))` → reducer → `useSelector`.

## Rules
- **Side effects live in sagas** (redux-saga is the async standard). No API calls in components,
  reducers or organisms.
- `takeLatest` for fetches/search/fetch-on-mount; `takeEvery` only for independent fire-and-forget;
  `takeLeading` for submit buttons that must not double-submit (payments!).
- Every request tracks `loading` / `error` / `data`; always handle the failure action in UI.
- Reducers are pure and immutable. Follow the store's existing style — if it uses RTK
  `createSlice`, use it; if it uses classic reducers, stay classic. Never mix within one store.
- Store only serialisable data (no class instances, functions, moment objects — store ISO strings).
- Don't store derived data; derive with selectors (`createSelector` for expensive derivations).
- Use the store's typed hooks / `RootState` type if present; otherwise type `useSelector` callbacks.
- Reset flow state on unmount/exit where the flow already does (maker/checker flows).
