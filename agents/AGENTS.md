# Axium Agent Guidelines

Canonical source: .github/copilot-instructions.md

## Code Style
- Follow existing Rust style and keep changes minimal and scoped.
- Prefer strongly typed request/response models in src/models instead of ad-hoc JSON maps.
- Keep handler return types consistent with existing patterns.
- Use tracing instrumentation for non-trivial handler and middleware logic.

## Architecture
- Request flow is routes -> auth wrapper or middleware -> handlers -> database/cache/storage/mail.
- Define endpoints in src/routes and map them to src/handlers.
- Use AuthenticatedRouteBuilder for protected routes.
- Keep SQL in src/database.
- Shared dependencies flow through AppState.

## Build and Test
- make app
- make app-build
- make app-release
- make migrate
- make migrate-status
- make docker-up
- make docker-app-up
- cargo test
- cargo clippy
- cargo fmt

## Conventions and Pitfalls
- Preserve RBAC role vectors.
- Preserve utoipa annotations.
- Return JSON errors under the error key.
- SQLx macros need compile-time schema metadata.
- Missing env vars can panic.
