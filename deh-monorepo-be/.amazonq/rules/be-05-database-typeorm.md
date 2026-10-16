# BE-05 · Database rules (TypeORM 0.3 + Postgres)

## Ownership
- Only **services** touch the DB. Gateways never import TypeORM.
- A service reads/writes only the tables it owns; data from another domain comes via that service's gRPC API.

## Entities & repositories
- `<domain>.entity.ts` + `<domain>.repository.ts` per domain; mirror the existing repository class
  pattern in the same app (custom repository wrapping TypeORM `Repository`/`DataSource`).
- Explicit column types: money → `numeric`/`decimal` (string in TS), timestamps → `timestamptz`,
  IDs per existing convention. Follow existing audit columns (created/updated by/at) on new tables.
- No business logic in entities or repositories — queries only.

## Queries
- Parameterised only: find options, or QueryBuilder with `:param` bindings.
  **Never** concatenate/interpolate values into SQL strings.
- Select only needed columns; always paginate list queries; avoid N+1 (joins / `In([...])`).
- Use `findOne` with explicit `where`; handle `null` → `RpcException(NOT_FOUND)`.

## Transactions
- Multi-statement writes (e.g. maker request + status history) run in one transaction:
  `this.dataSource.transaction(async (manager) => { … })` or a `QueryRunner` with commit/rollback/release.
- Don't call external services/gRPC inside an open DB transaction.

## Migrations
- `synchronize: true` is forbidden in every environment.
- Every schema change (tables, columns, indexes) ships as a migration, reviewed with the PR.
- The package.json migration scripts use TypeORM 0.2-style flags (`ormconfig.json`, `-n`) while
  the dependency is 0.3.x, and an `apps/migration-scripts` app exists — **confirm the current
  migration workflow with the developer before generating or running migrations.**
- Never edit a migration that has already run in any shared environment; add a new one.
