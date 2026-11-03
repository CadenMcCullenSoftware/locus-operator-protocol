# Repository Structure Guide

## Recommended Top-Level Layout

```
project-root/
├── src/                  # Application/library source code
├── tests/                # Test suites (unit, integration, e2e)
├── docs/                 # Documentation (guides, API, ADRs)
├── config/               # Configuration files (non-secret)
├── scripts/              # Build, deploy, utility scripts
├── assets/               # Static assets (images, fonts)
├── .github/              # GitHub workflows, issue/PR templates
├── .vscode/              # Editor settings (optional, committed)
├── dist/                 # Build output (gitignored)
├── node_modules/         # Dependencies (gitignored)
├── .gitignore
├── .env.example          # Template for environment variables (committed)
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── SECURITY.md
└── CHANGELOG.md
```

## Directory Conventions

| Directory | Purpose | Git-tracked? |
|---|---|---|
| `src/` | All production source code, organized by feature or layer | Yes |
| `tests/` | Mirrors `src/` structure; one test file per source module | Yes |
| `docs/` | Human-readable docs, architecture decision records (ADRs) | Yes |
| `config/` | Environment-agnostic configuration (YAML/JSON/TOML) | Yes |
| `scripts/` | Automation scripts (build, release, migrations) | Yes |
| `assets/` | Static, non-code resources | Yes |
| `dist/` / `build/` | Compiled/bundled output | No (gitignored) |
| `node_modules/` / `vendor/` | Installed dependencies | No (gitignored) |

## Source Code Organization (`src/`)

Prefer **feature-based** over layer-based grouping for scalability:

```
src/
├── features/
│   ├── auth/
│   │   ├── auth.controller.ts
│   │   ├── auth.service.ts
│   │   ├── auth.repository.ts
│   │   └── auth.test.ts
│   └── billing/
├── shared/               # Cross-cutting utilities
│   ├── logger.ts
│   └── errors.ts
└── index.ts              # Public entry point / barrel export
```

## Essential Configuration Files

### `.gitignore`
Ignore build artifacts, dependencies, secrets, and OS/editor noise:

```
# Dependencies
node_modules/
vendor/
.venv/

# Build output
dist/
build/
out/
*.log

# Environment & secrets
.env
.env.local
*.pem
*.key

# OS / editor
.DS_Store
Thumbs.db
.idea/
*.swp
```

### `README.md`
Must include: project name, one-line description, badges, prerequisites, install, usage, tests, contributing, license.

### `LICENSE`
Choose a license (MIT, Apache-2.0, GPL-3.0) and commit the full text. Required for open source.

### `.env.example`
Committed template listing required environment variables with placeholder values. Never commit real `.env`.

## Best Practices

1. **Separate source from build artifacts** — keep `src/` pure; output goes to `dist/` or `build/`, both gitignored.
2. **Dependencies are not source** — pin versions in a lockfile (`package-lock.json`, `poetry.lock`, `Cargo.lock`) and gitignore the install directory.
3. **Docs live with code** — co-locate module docs or use `docs/`; keep README at root minimal.
4. **Config is code** — version environment-agnostic config; secrets go in env vars / secret managers.
5. **One concern per file** — small, focused modules; barrel exports (`index.ts`) for public API.
6. **Tests mirror source** — `tests/features/auth/...` mirrors `src/features/auth/...`.
7. **Automate hygiene** — pre-commit hooks (lint, format, tests) via Husky/lefthook.
8. **ADRs** — record architectural decisions in `docs/adr/0001-*.md`.

## Template Scaffold

Copy this skeleton to bootstrap a new project:

```
mkdir -p src/{features,shared} tests docs config scripts assets .github/workflows
touch README.md LICENSE .gitignore .env.example CONTRIBUTING.md
```

## Scaling Notes

- **Monorepo**: use `packages/` or `apps/` + `packages/` with a workspace tool (pnpm workspaces, Turborepo, Nx).
- **Microservices**: one repo per service, or monorepo with `services/` + shared `libs/`.
- **Libraries**: expose only `src/index.*` as public API; keep internals under `src/internal/`.

---

*End of Repository Structure Guide.*
EOF
echo done