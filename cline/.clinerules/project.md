# Axium Cline Project Rules

- Primary source: .github/copilot-instructions.md
- Request flow: routes -> auth wrapper or middleware -> handlers -> database/integrations.
- Route registration in src/routes, handler logic in src/handlers, DTOs in src/models, SQL in src/database.
- Use AppState for shared dependencies and AuthenticatedRouteBuilder for protected routes.
- Preserve validator usage, RBAC vectors, utoipa annotations, and JSON error responses under the error key.
- Commands: make app, make app-build, make app-release, make migrate, cargo test, cargo clippy, cargo fmt.
- Pitfalls: SQLx compile-time metadata and missing env vars.
