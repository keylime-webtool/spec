# Keylime Monitoring Dashboard — Specifications

Formal requirements and design documents for the
[Keylime Monitoring Dashboard](https://github.com/keylime-webtool) project.

## Documents

| Document | Description |
|----------|-------------|
| [SRS-Keylime-Monitoring-Tool.md](SRS-Keylime-Monitoring-Tool.md) | Software Requirements Specification — 70 functional, 23 non-functional, and 29 security requirements with Gherkin acceptance criteria. Includes implementation refinements (Section 7) tracking data models, API contracts, and enumerations that emerged during development. |
| [SDD-Keylime-Monitoring-Tool.md](SDD-Keylime-Monitoring-Tool.md) | Software Design Description (IEEE 1016-2009) — architecture, component decomposition, data models, API contracts, interaction patterns, state machines, and deployment topology. |

## Methodology

These specifications follow **Spec-Driven Development (SDD)**:

1. Requirements are written first using RFC 2119 keywords (MUST, SHALL, SHOULD, MAY)
2. Each requirement includes Gherkin `Given/When/Then` acceptance criteria
3. Requirements are tagged: `FR-NNN` (functional), `NFR-NNN` (non-functional), `SR-NNN` (security)
4. Implementation refinements are tracked back into the SRS as they emerge

## License

This work is licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
