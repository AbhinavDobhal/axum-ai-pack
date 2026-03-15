# Project Guidelines

## Code Style
- Follow existing Rust style in this repository and keep changes minimal and scoped.
- Prefer strongly typed request/response models in src/models instead of ad-hoc JSON maps.
- Keep handler return types consistent with existing patterns: Result<Json<T>, (StatusCode, Json<serde_json::Value>)>.
- Use tracing instrumentation for non-trivial handler and middleware logic.

## Architecture
- Request flow is: routes -> authentication wrapper/middleware -> handlers -> database/cache/storage/mail modules.
- Define endpoints in src/routes and map them to handler functions in src/handlers.
- Use AuthenticatedRouteBuilder in src/wrappers/authentication_route_builder.rs for authenticated routes and RBAC role vectors.
- Keep persistence logic in src/database and avoid embedding SQL in handlers.
- Shared application dependencies are passed via AppState in src/routes/mod.rs.

## Build And Test
- Primary local commands:
  - make app
  - make app-build
  - make app-release
  - make migrate
  - make migrate-status
  - make docker-up
  - make docker-app-up
- Standard Rust checks when relevant: cargo test, cargo clippy, cargo fmt.

## Conventions
- Route protection is role-level based with vectors like vec![1, 2]; preserve existing RBAC semantics.
- Validate incoming payloads with validator derives and return JSON errors under the error key.
- Preserve utoipa OpenAPI annotations on handlers when modifying endpoints.
- Keep DTO separation clear between DB rows, request bodies, and response structs.
- Keep sensitive fields protected from serialization.

## Environment And Pitfalls
- SQLx macro queries require schema metadata during compile/check.
- Prefer runtime sqlx::query or query_scalar in new modules when compile-time metadata is unavailable.
- Many config reads are required and may panic if env vars are missing.
- Ensure migrations are in place before relying on new database fields or tables.
