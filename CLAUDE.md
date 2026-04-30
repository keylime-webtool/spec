# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a **specification-only repository** for the [Keylime Monitoring Dashboard](https://github.com/keylime-webtool) project. It contains no application code — only formal requirements and design documents written in Markdown.

## Documents

| Document | Lines | Description |
|----------|-------|-------------|
| `SRS-Keylime-Monitoring-Tool.md` | ~4,000 | Software Requirements Specification — 85 FR, 24 NFR, 29 SR with Gherkin acceptance criteria |
| `SDD-Keylime-Monitoring-Tool.md` | ~1,200 | Software Design Description (IEEE 1016-2009) — architecture, components, data models, API contracts |

### SRS Structure

1. Core Objective
2. Requirements Inventory Table (FR/NFR/SR summary with traceability)
3. Detailed Functional Requirements (FR-001 through FR-086)
4. Non-Functional Requirements Detail (NFR-001 through NFR-024)
5. Security Requirements Detail (SR-001 through SR-029)
6. Implementation Phasing
7. Software Design Description Cross-Reference

### SDD Structure

1. Introduction
2. Design Viewpoints (IEEE 1016 viewpoint definitions)
3. Design Views (Context, Composition, Logical, Interface, Interaction, State, Algorithm, Resource)
4. Design Rationale
5. Design Overlays
6. SRS Traceability Matrix

## Methodology

Spec-Driven Development (SDD) with strict conventions:

- **RFC 2119 keywords**: MUST, SHALL, SHOULD, MAY — used precisely per their RFC definitions
- **Requirement tags**: `FR-NNN` (functional), `NFR-NNN` (non-functional), `SR-NNN` (security)
- **Gherkin acceptance criteria**: Every requirement includes `Given/When/Then` scenarios
- **Cross-references**: Requirements reference each other inline (e.g., `(FR-001)`, `(SR-004)`)
- **Traceability**: SDD elements trace back to SRS requirements; SRS Section 7 cross-references the SDD

When editing requirements, maintain all of these conventions. New requirements get the next sequential ID in their category.

## Linting

```bash
npx markdownlint-cli2 "**/*.md"    # lint all markdown files
```

Configuration is in `.markdownlint-cli2.yaml`. Key overrides for spec documents:

- **MD013 (line length)**: disabled — spec tables and descriptions exceed 80 chars
- **MD024 (duplicate headings)**: `siblings_only` — FR sections repeat patterns like "Description", "Trace"
- **MD026 (trailing punctuation)**: disabled — headings use `FR-001: Title` format
- **MD033 (inline HTML)**: disabled
- **MD060 (table separator)**: disabled — allows compact `|---|---|` style

## CI Workflows

- **markdown-lint.yaml**: Runs `markdownlint-cli2` on push to `main` and all PRs. Smart-filters to only lint when `.md` files, the lint config, or the workflow itself changed.
- **signed-commits.yml**: Enforces GPG-signed commits on PRs to `main` (delegates to shared workflow in `keylime-webtool/.github`).

## Companion Repositories

This spec drives implementation in two sibling repos (read/reference only from here):

- `keylime-webtool-backend` — Rust/Axum REST + WebSocket API server
- `keylime-webtool-frontend` — TypeScript/React 18 + Vite SPA

The upstream Keylime projects (`keylime`, `rust-keylime`) are consumed via their REST APIs. Their API docs live at `keylime/docs/rest_apis/`.
