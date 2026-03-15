# Axum Cross-IDE AI Pack

Adding a new route to a Rust backend should take 20 minutes. In practice, it often takes two hours — not because the code is hard to write, but because you spend most of the time rediscovering where the code lives.

You need to find the route file. Then figure out the handler split pattern. Then locate the DB module. Then understand how DTOs are structured in this repo. Then remember how authenticated endpoints are wired. By the time you actually write the handler, you've made three wrong assumptions and will fix them in code review.

That is the real Rust speed problem. Not compile times. Context loss.

The AI pack pattern for Rust backends — specifically for Axum — is a direct fix for that problem. This repository is a working implementation of that pattern you can clone and adapt.

---

## Table of Contents

- [Why "Rust Is Slow to Develop" Is Half the Story](#why-rust-is-slow-to-develop-is-half-the-story)
- [What an AI Pack Actually Is](#what-an-ai-pack-actually-is)
- [Where the Time Actually Goes](#where-the-time-actually-goes)
- [Why Rust Benefits More Than Node.js or Python](#why-rust-benefits-more-than-nodejs-or-python)
- [Rust vs. FastAPI vs. Node.js: What Actually Changes With a Pack](#rust-vs-fastapi-vs-nodejs-what-actually-changes-with-a-pack)
- [What a Reconstruction Playbook Looks Like](#what-a-reconstruction-playbook-looks-like)
- [What the AI Pack Does Not Do](#what-the-ai-pack-does-not-do)
- [The Real Compounding Effect](#the-real-compounding-effect)
- [Using This Pack](#using-this-pack)

---

## Why "Rust Is Slow to Develop" Is Half the Story

The dynamic-stack crowd has a point: building a CRUD endpoint in FastAPI or Node.js is faster on day one. Less type friction, no borrow checker, shorter boilerplate.

But that advantage is mostly about discovery overhead — the cost of figuring out what goes where. In a typed, structured Rust codebase, that overhead doesn't go away. It compounds. Every new contributor, every new AI assistant session, every context switch resets the clock.

Here's where it gets worse: AI coding assistants make this problem structural, not just personal. GitHub Copilot, Claude, Cursor, and Windsurf all operate without persistent project memory. Each session starts cold. Ask your assistant to add a user-owned CRUD module, and without project context, it will generate something reasonable for a generic Axum tutorial — not something that fits your actual repo. The types won't align. The module boundaries will be wrong. The RBAC assumptions won't match.

You then spend 40 minutes cleaning up code that was generated in 2 minutes.

---

## What an AI Pack Actually Is

An AI pack is a structured set of AI-facing project artifacts that give assistants the same architectural memory a senior engineer carries in their head.

For an Axum Rust backend, that means files that specify:

| Artifact | What it provides |
|---|---|
| **Folder structure snapshot** | Exactly where routes, handlers, models, and database modules live |
| **Module responsibility map** | Which layer owns what, and why |
| **Endpoint and RBAC reference** | How authenticated routes are wired, which access vectors are expected |
| **Schema reference from migrations** | The current database shape derived from migration history |
| **Environment variable catalog** | Every required config key, what it does, where it's used |
| **Rebuild playbook** | Step-by-step commands to recreate the project from scratch in a new repo |
| **Cross-IDE mirrors** | The same canonical guidance exported for every tool your team uses |

The last point matters more than people expect. Without a pack, different AI tools drift. One assistant learns your architecture through accumulated context. Another reverts to generic framework habits. The cross-IDE mirror prevents that — one canonical source, consistent output across every tool your team uses.

### Included mirrors

| Tool | File location in this pack | Deploy to |
|---|---|---|
| GitHub Copilot | `copilot/.github/copilot-instructions.md` | `.github/copilot-instructions.md` |
| Claude | `claude/CLAUDE.md` | `CLAUDE.md` at repo root |
| Cursor | `cursor/.cursorrules` | `.cursorrules` at repo root |
| Windsurf | `windsurf/.windsurfrules` | `.windsurfrules` at repo root |
| Cline | `cline/.clinerules/project.md` | `.clinerules/project.md` |
| AGENTS-compatible tools | `agents/AGENTS.md` | `AGENTS.md` at repo root |

The canonical source is `copilot/.github/copilot-instructions.md`. Update that first, then regenerate the mirrors.

---

## Where the Time Actually Goes

Let's be specific about where backend delivery time is actually lost.

**Context rebuilding per task** is the most expensive hidden cost. Before adding a single endpoint, an engineer — or their assistant — has to rediscover the route file, handler split convention, DB module location, request/response struct style, migration expectations, and auth pattern. In a mature codebase, that discovery can take longer than the code change itself. The AI pack compresses that overhead into references an assistant can load immediately.

**Architectural drift from AI-generated code** is costly to fix later. When an assistant guesses at structure, it creates files in the wrong place, duplicates logic, misses RBAC rules, makes incorrect schema assumptions, and introduces env config mismatches. Preventing that bad output is more valuable than generating output quickly.

**Project replication without a blueprint** consistently produces broken copies. When you start a second service using the same architecture, without a structured pack, you get a codebase that looks similar but differs in the ways that matter — incomplete migrations, incorrect middleware, simplified auth, different command conventions. The pack makes replication reliable.

---

## Why Rust Benefits More Than Node.js or Python

In JavaScript or Python, vague AI-generated code can often still run. Types are flexible, module boundaries are loose, and wrong assumptions often surface only at runtime — if at all.

In Rust, incorrect assumptions fail at compile time. Wrong module wiring, mismatched types, ownership problems, response types that don't align with repo conventions — the compiler catches them immediately. That sounds like a feature (it is), but it also means poorly context-aware AI output produces code that doesn't even compile, let alone pass review.

The AI pack narrows the gap between AI-generated output and compilable, architecture-aligned code. When the assistant knows how `AppState` is passed, how handlers return typed responses, where SQLx queries live, and how authenticated routes are wrapped, its output is far closer to correct on the first pass.

Generic Axum tutorial output is especially harmful here. It wires things in ways that are textbook-correct but repo-wrong. The AI pack replaces tutorial habits with repo semantics.

---

## Rust vs. FastAPI vs. Node.js: What Actually Changes With a Pack

**Without an AI pack**, dynamic stacks win the early game:

| | FastAPI / Node.js | Rust + Axum |
|---|---|---|
| Day-one endpoint | Fast — minimal boilerplate | Slower — type setup overhead |
| Second contributor | Moderate discovery cost | High discovery cost |
| AI assistant output | Usually runnable | Often fails to compile |
| Architectural drift | Surfaces at runtime | Surfaces at compile time or review |

**With an AI pack**, the picture shifts for structured backends:

| | FastAPI / Node.js | Rust + Axum + AI Pack |
|---|---|---|
| Day-one endpoint | Fast | Moderate |
| Second contributor | Moderate discovery cost | Low — pack answers most questions |
| AI assistant output | Usually runnable | Closer to correct on first pass |
| Architectural drift | Surfaces at runtime | Caught early, prevented by context |
| Long-term consistency | Relies on discipline | Enforced by shared pack |

The honest answer: FastAPI still wins when the team is Python-native and the product is in rapid early validation. Node.js wins when full-stack TypeScript code sharing matters. Rust plus a strong AI pack wins when the backend needs to live for years, handle real concurrency, and stay architecturally consistent across a team with mixed tooling preferences.

---

## What a Reconstruction Playbook Looks Like

One of the most useful pieces of the AI pack is the rebuild playbook. Here's what a minimal version covers for an Axum project:

```bash
# Rebuild Playbook: Axum Backend

## 1. Project Init
cargo new --bin service-name
cd service-name

## 2. Core Dependencies (Cargo.toml)
# axum, tokio (full features), sqlx (runtime-tokio-rustls, postgres, macros, migrate),
# serde (derive), serde_json, dotenvy, uuid (v4, serde), anyhow

## 3. Folder Structure
# src/
#   main.rs         — startup, AppState init, router composition
#   routes/         — one file per resource group
#   handlers/       — pure request/response logic, no DB
#   db/             — all SQLx queries, one module per entity
#   models/         — shared types: DTOs, domain structs
#   middleware/     — auth extractors, RBAC checks

## 4. Migration Setup
sqlx migrate add create_users
# Run: sqlx migrate run

## 5. AppState Pattern
# #[derive(Clone)]
# pub struct AppState { pub db: PgPool }
# Pass via Router::with_state(state)

## 6. Auth Middleware
# Implement FromRequestParts for authenticated extractor
# Use in handler signature: auth: AuthenticatedUser

## 7. Verification
cargo build
cargo test
cargo sqlx prepare --check
```

That playbook, stored in the pack and readable by any assistant, means the second service doesn't start from a blank tutorial. It starts from the same architecture the first one used.

---

## What the AI Pack Does Not Do

- It does not replace engineering judgment.
- It does not guarantee the generated code compiles or passes review.
- It does not make Rust's learning curve disappear.
- It does not make every task literally faster — some tasks are just research.

What it does is remove avoidable friction from the tasks where friction comes from context loss, not from genuine complexity. That is a narrower and more honest claim. It also happens to be where a significant chunk of backend delivery time actually goes.

---

## The Real Compounding Effect

The strongest case for this approach isn't one engineer working faster. It's five engineers using three different AI tools all getting project-aware output — because the pack is shared and the cross-IDE mirrors are current.

That's where the leverage appears: not in the speed of typing, but in the consistency of decisions made across a team over months.

If you're running an Axum backend that's going to grow, start the AI pack before you have five contributors, not after. The architectural memory is easiest to capture while it's still fresh.

---

## Using This Pack

### Deploy mirrors to your project

```sh
# GitHub Copilot
cp copilot/.github/copilot-instructions.md .github/copilot-instructions.md

# Claude
cp claude/CLAUDE.md CLAUDE.md

# Cursor
cp cursor/.cursorrules .cursorrules

# Windsurf
cp windsurf/.windsurfrules .windsurfrules

# Cline
cp -r cline/.clinerules .clinerules

# AGENTS-compatible tools
cp agents/AGENTS.md AGENTS.md
```

### Sync policy

1. Edit the canonical source: `copilot/.github/copilot-instructions.md`
2. Propagate changes to all mirror files
3. Commit all mirrors together so they stay in sync

### Zip for export

This folder is structured to be zipped directly. Every mirror file is at its correct relative path so it can be extracted into any Axum project root.

```sh
zip -r axum-ai-pack.zip . -x "*.git*"
```
