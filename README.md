# Keylime Monitoring Dashboard — Specifications

Formal requirements and design documents for the
[Keylime Monitoring Dashboard](https://github.com/keylime-webtool) project.

## Documents

| Document | Description |
|----------|-------------|
| [SRS-Keylime-Monitoring-Tool.md](SRS-Keylime-Monitoring-Tool.md) | Software Requirements Specification — 93 functional, 28 non-functional, and 30 security requirements with Gherkin acceptance criteria. Section 7 cross-references the companion SDD, where implementation refinements (IR-001–IR-023) that emerged during development are documented. |
| [SDD-Keylime-Monitoring-Tool.md](SDD-Keylime-Monitoring-Tool.md) | Software Design Description (IEEE 1016-2009) — architecture, component decomposition, data models, API contracts, interaction patterns, state machines, and deployment topology. |

## Methodology

These specifications follow **Spec-Driven Development (SDD)**:

1. Requirements are written first using RFC 2119 keywords (MUST, SHALL, SHOULD, MAY)
2. Each requirement includes Gherkin `Given/When/Then` acceptance criteria
3. Requirements are tagged: `FR-NNN` (functional), `NFR-NNN` (non-functional), `SR-NNN` (security)
4. Implementation refinements are documented in the companion SDD and cross-referenced from SRS Section 7

## License

This work is licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
