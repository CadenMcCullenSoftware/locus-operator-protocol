# Repository Structure Guide

**Version:** 1.0
**Scope:** Standard for all software projects maintained by the development team.
**How to use:** Copy this document into a project's `docs/` directory, adapt sections marked *(adapt)*, and treat the checklists as Definition-of-Done criteria.

---

## 1. Recommended Directory Structure

```
project-root/
├── src/                     # Application / library source code
├── tests/                   # Automated test suites
├── docs/                    # Human-readable documentation
├── config/                  # Environment-agnostic configuration
├── scripts/                 # Build, release, migration, utility scripts
├── assets/                  # Static non-code resources
├── .github/                 # GitHub workflows, issue/PR templates
├── .vscode/                 # Editor settings (optional, committed)
├── dist/                    # Build output (gitignored)
├── node_modules/            # Installed dependencies (gitignored)
├── .gitignore
├── .env.example             # Committed env-var template
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── SECURITY.md
└── CHANGELOG.md
```

### Per-Folder Purpose

| Folder | Purpose | Tracked? |
|---|---|---|
| `src/` | All production source code, organized by feature or layer | Yes |
| `tests/` | Unit, integration, and e2e suites mirroring `src/` | Yes |
| `docs/` | Guides, API reference, ADRs, diagrams | Yes |
| `config/` | Environment-agnostic configuration (YAML/JSON/TOML) | Yes |
| `scripts/` | Automation: build, release, DB migrations, dev tools | Yes |
| `assets/` | Static resources (images, fonts, fixtures) | Yes |
| `.github/` | CI/CD workflows, issue/PR templates, CODEOWNERS | Yes |
| `.vscode/` | Shared editor settings and recommended extensions | Yes (optional) |
| `dist/` / `build/` / `out/` | Compiled or bundled output | **No** (gitignored) |
| `node_modules/` / `vendor/` / `.venv/` | Installed dependencies | **No** (gitignored) |

### Source Organization (`src/`)

Prefer **feature-based** grouping over layer-based for scalability:

```
src/
├── features/
│   ├── auth/
│   │   ├── auth.controller.ts
│   │   ├── auth.service.ts
│   │   ├── auth.repository.ts
│   │   └── index.ts          # Barrel export (public API)
│   └── billing/
├── shared/                   # Cross-cutting utilities
│   ├── logger.ts
│   └── errors.ts
└── index.ts                  # Library / app entry point
```

---

## 2. Essential Root-Level Files

### `.gitignore`

Ignore build artifacts, dependencies, secrets, and OS/editor noise. Baseline:

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

**Rule:** If a file is generated or contains secrets, it must be gitignored.

### `README.md`

Must include, in order:
1. Project name + one-line description
2. Badges (build, coverage, license, version)
3. Prerequisites
4. Installation
5. Usage (minimal working example)
6. Running tests
7. Contributing pointer (`CONTRIBUTING.md`)
8. License

### `LICENSE`

Commit the full license text. Required for any open-source or shared project. Common choices: MIT (permissive), Apache-2.0 (patent grant), GPL-3.0 (copyleft).

### `.env.example`

Committed template listing required environment variables with placeholder values. Never commit real `.env`. Document each variable's purpose in a comment.

### `CONTRIBUTING.md`

Branching strategy, commit message convention, PR process, code-style rules, how to run tests locally.

### `SECURITY.md`

How to report vulnerabilities (private disclosure), supported versions, response SLA.

### `CHANGELOG.md`

Keep a Changelog format (Added/Changed/Deprecated/Removed/Fixed/Security), updated per release.

### CI/CD Workflows (`.github/workflows/`)

At minimum: `ci.yml` (lint + test + build on PR/push) and `release.yml` (tag-triggered release). Pin action versions by SHA for supply-chain safety.

---

## 3. Separation Guidelines

### Source vs. Build Artifacts

- `src/` contains only hand-authored source. Output goes to `dist/`/`build/`/`out/`.
- Build directories are gitignored and cleaned on every build (`rm -rf dist && build`).
- Never edit files inside build output; always regenerate.

### Source vs. Dependencies

- Dependencies are declared in a manifest (`package.json`, `pyproject.toml`, `Cargo.toml`) and pinned via a lockfile (`package-lock.json`, `poetry.lock`, `Cargo.lock`).
- The install directory (`node_modules/`, `vendor/`, `.venv/`) is gitignored.
- Commit the lockfile; never commit the install directory.

### Source vs. Documentation

- Keep `README.md` at root minimal; detailed docs live in `docs/`.
- Co-locate module-level docs next to code (`src/features/auth/README.md`) **or** centralize in `docs/` — pick one and stay consistent.
- Architecture Decision Records (ADRs) go in `docs/adr/0001-*.md`.

### Source vs. Configuration

- Environment-agnostic config → `config/` (tracked).
- Environment-specific secrets → env vars / secret manager (never tracked).
- `.env.example` is the contract; `.env` is local-only.

### Enforcement

- Pre-commit hooks (Husky/lefthook) run lint, format, and tests before commit.
- CI gates: lint + typecheck + tests + build must pass before merge.
- CODEOWNERS enforces review on critical paths.

---

## 4. Scalability Considerations by Project Type

### Monolithic Application

- Single `src/` with feature folders.
- One deployable artifact; scale via horizontal replication.
- Add `migrations/` and `seeds/` under `scripts/` or `db/`.

### Microservices

- **Option A — polyrepo:** one repo per service (simplest ownership).
- **Option B — monorepo:** `services/<name>/` + shared `packages/<lib>/`, orchestrated by Turborepo/Nx/pnpm workspaces.
- Shared contracts (OpenAPI/protobuf) in `packages/contracts/`.
- Per-service `Dockerfile` and `docker-compose.yml` at service root.

### Library / Package

- Public API = `src/index.*` only; internals under `src/internal/`.
- Include `examples/`, `benchmarks/`, and published type definitions.
- Version via SemVer; changelog is mandatory.

### Application (Frontend / Full-stack)

- `src/components/`, `src/pages/` or `src/routes/`, `src/styles/`.
- Asset pipeline output to `dist/`/`public/` (gitignored).
- Environment config via `.env.*` files per environment.

### Monorepo Decision Criteria

Choose monorepo when: shared code changes frequently, atomic cross-package refactors are needed, or unified CI is desired. Choose polyrepo when: teams are autonomous, release cycles differ, or blast-radius isolation matters.

---

## 5. Bootstrap Scaffold

```bash
mkdir -p src/{features,shared} tests docs config scripts assets .github/workflows
touch README.md LICENSE .gitignore .env.example CONTRIBUTING.md CODE_OF_CONDUCT.md SECURITY.md CHANGELOG.md
```

### Definition-of-Done Checklist

- [ ] Directory layout matches Section 1
- [ ] `.gitignore` covers all generated/secret files
- [ ] `README.md` contains all required sections (Section 2)
- [ ] `LICENSE` committed with full text
- [ ] `.env.example` committed; `.env` gitignored
- [ ] CI workflow present and passing
- [ ] Pre-commit hooks configured
- [ ] `CONTRIBUTING.md` documents branch + commit conventions

---

## Appendix A — Naming Conventions

- Directories: `kebab-case` (e.g., `user-profile`).
- Source files: follow language convention (`camelCase.ts`, `snake_case.py`, `PascalCase.cs`).
- Test files: `<module>.test.<ext>` or `<module>_test.<ext>`.
- ADRs: `docs/adr/0001-short-title.md` (zero-padded, sequential).

## Appendix B — Commit Messages

Follow Conventional Commits: `<type>(<scope>): <subject>` where type ∈ {feat, fix, docs, style, refactor, test, chore, perf, ci}. Subject in imperative mood, ≤72 chars.

## Appendix C — Branch Strategy

- `main` — production-ready, protected.
- `develop` — integration (GitFlow) or feature branches merge directly to `main` (trunk-based).
- Feature branches: `feat/<ticket>-short-desc`.
- Release branches: `release/vX.Y.Z`.
- Hotfix branches: `hotfix/<issue>`.

---

*End of Repository Structure Guide.*
EOF
echo done