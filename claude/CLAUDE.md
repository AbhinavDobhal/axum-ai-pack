# Axium Claude Guidance

Source of truth: .github/copilot-instructions.md

- Keep the layered architecture intact: routes -> auth -> handlers -> database/integrations.
- Put route registration in src/routes, endpoint logic in src/handlers, DTOs in src/models, and SQL in src/database.
- Use AppState for shared dependencies.
- Use AuthenticatedRouteBuilder for authenticated routes.
- Preserve RBAC role vectors and utoipa annotations.
- Use validator derives and JSON error responses under the error key.
- Key commands: make app, make app-build, make migrate, make migrate-status, cargo test, cargo clippy, cargo fmt.
- Watch for SQLx metadata requirements and missing env vars.
