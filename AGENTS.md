# AGENTS.md

Guidance for AI coding agents working in this repository.

## Repository Purpose

This is a **specification-only repository** for the [Keylime Monitoring Dashboard](https://github.com/keylime-webtool) project. It contains no application code — only formal requirements and design documents written in Markdown. The Markdown documents *are* the product; treat every edit with the care you would give production code.

## Documents

| Document | Size | Description |
|----------|------|-------------|
| `SRS-Keylime-Monitoring-Tool.md` | ~4,550 lines | Software Requirements Specification — functional (FR), non-functional (NFR), and security (SR) requirements with Gherkin acceptance criteria |
| `SDD-Keylime-Monitoring-Tool.md` | ~1,650 lines | Software Design Description (IEEE 1016-2009) — architecture, components, data models, API contracts, state machines, deployment topology |

Requirement ID ranges: `FR-001`–`FR-095`, `NFR-001`–`NFR-028`, `SR-001`–`SR-030`. Numbering has historical gaps (e.g., FR-040 and FR-053 are unused) — never assume contiguous IDs; derive counts from the Requirements Inventory Table (SRS Section 2) instead of hardcoding them.

### SRS Structure

1. Core Objective
2. Requirements Inventory Table (FR/NFR/SR summary with traceability sources)
3. Detailed Functional Requirements
4. Non-Functional Requirements Detail
5. Security Requirements Detail
6. Implementation Phasing
7. SDD Cross-Reference (maps former Implementation Refinements IR-001–IR-023 to SDD sections)

### SDD Structure

1. Introduction
2. Design Viewpoints (IEEE 1016 viewpoint definitions)
3. Design Views (Context, Composition, Logical, Interface, Interaction, State, Algorithm, Resource)
4. Design Rationale
5. Design Overlays (Security, Performance)
6. SRS Traceability Matrix (every requirement mapped to design elements)

## Methodology

Spec-Driven Development with strict conventions:

- **RFC 2119 keywords**: MUST, SHALL, SHOULD, MAY — used precisely per their RFC definitions
- **Requirement tags**: `FR-NNN` (functional), `NFR-NNN` (non-functional), `SR-NNN` (security)
- **Gherkin acceptance criteria**: every requirement includes `Given/When/Then` scenarios in fenced ```` ```gherkin ```` blocks
- **Cross-references**: requirements reference each other inline (e.g., `(FR-001)`, `(SR-004)`)
- **Traceability**: SRS Section 2 lists traceability sources; SDD Section 6 maps requirements to design elements; SRS Section 7 maps IRs to SDD sections

## Editing Conventions

When adding or changing requirements, maintain **all** of the following:

1. **New requirement IDs**: use the next sequential ID after the current maximum in that category (do not fill historical gaps).
2. **Inventory table**: add a row to the Requirements Inventory Table (SRS Section 2) with Req ID, description, RFC level, and traceability source.
3. **Full requirement format** (see existing entries for the pattern):
   - `### FR-NNN: Title` heading
   - `**Description:**` paragraph with RFC 2119 keywords
   - `**Trace:**` line pointing to the source presentation section
   - Gherkin `Feature`/`Scenario` block with concrete, testable outcomes
4. **Cross-document consistency**: if a change affects design, update the SDD (and its Section 6 traceability matrix); if it affects the SRS↔SDD mapping, update SRS Section 7.
5. **Change annotations**: mark substantive additions with HTML comments, e.g. `<!-- CHANGED: Added IR-020 for background attestation recording -->`.
6. **Requirements are scoped to Keylime**: terminology (agent states, Verifier/Registrar APIs, TPM/IMA concepts) must match upstream Keylime behavior, not generic monitoring-tool assumptions.

## Validation

```bash
npx markdownlint-cli2 "**/*.md"    # lint all markdown files
```

Run this before every commit; CI runs the same check. Configuration lives in `.markdownlint-cli2.yaml`. Key overrides for spec documents:

- **MD013 (line length)**: disabled — spec tables and descriptions exceed 80 chars
- **MD024 (duplicate headings)**: `siblings_only` — FR sections repeat patterns like "Description", "Trace"
- **MD026 (trailing punctuation)**: disabled — headings use `FR-001: Title` format
- **MD033 (inline HTML)**: disabled — used for `<!-- CHANGED: ... -->` annotations
- **MD060 (table separator)**: disabled — allows compact `|---|---|` style

## CI Workflows

- **markdown-lint.yaml**: runs `markdownlint-cli2` on push to `main` and all PRs; smart-filters to only lint when `.md` files, the lint config, or the workflow itself changed.
- **signed-commits.yml**: enforces GPG-signed commits on PRs to `main` (delegates to the shared workflow in `keylime-webtool/.github`).

## Git Workflow

- Fork-based: `origin` is the personal fork (`sarroutbi/keylime-webtool-spec`), `upstream` is `keylime-webtool/spec`. All changes land via PRs to `upstream` `main`.
- Branch names: short, descriptive, kebab-case (e.g., `align-sdd-certificate-parsing`).
- Every commit must be signed (`git commit -s` or GPG) — CI rejects unsigned PR commits.
- Commit messages: imperative subject line; do not include PR/issue numbers — GitHub appends them on merge.

## Companion Repositories

This spec drives implementation in two sibling repos (read/reference only from here):

- `keylime-webtool-backend` — Rust/Axum REST + WebSocket API server
- `keylime-webtool-frontend` — TypeScript/React 18 + Vite SPA

The upstream Keylime projects (`keylime`, `rust-keylime`) are consumed via their REST APIs; their API docs live at `keylime/docs/rest_apis/`.

## Related Agent Files

`CLAUDE.md` provides equivalent guidance for Claude Code. When changing agent-facing conventions in this file, update `CLAUDE.md` (and the README document table if scope changes) to keep them in sync.
