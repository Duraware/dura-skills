# Mode D: Architecture Decision Records

Part of the `architect` skill. Loaded on demand from SKILL.md.

## D. ADR
One decision per ADR, one to two pages, Markdown, one file each, stored in a single system of record (a repo folder such as `docs/adr/`, a dedicated ADR repo or a wiki). Follow the project's existing ADR template and numbering if it has one; otherwise use this one. Never paste the decision itself into email or chat; link to it.

```markdown
# ADR-NNNN: <short, unambiguous title, e.g. "Use asynchronous messaging between Order and Payment services">

## Status
Request for Comments (deadline YYYY-MM-DD) | Proposed | Accepted | Superseded by ADR-NNNN
(when superseding: "Accepted, supersedes ADR-NNNN")

## Context
The forces at play: what situation forces this decision? Briefly name the alternatives.

## Decision
"We will ..." (affirmative, commanding voice), with full justification: technical AND business reasons.

## Alternatives (optional; use when alternatives need detailed analysis)
Each option and why it was rejected.

## Consequences
Positive and negative impact; the trade-off analysis and what was explicitly accepted.

## Compliance
How the decision is measured and governed: manual review or an automated fitness function (describe it).

## Notes
Author, approved by, approval date, superseded date, last modified (date/by/what).
```

Rules:
- Proposed ADRs are edited until accepted; **accepted ADRs are never edited**, only superseded.
- Agree approval criteria up front (cost threshold, cross-team impact, security impact means higher-level approval).
- ADRs can also record **standards**, with the *why* explained, and can be written **retroactively** for existing systems to recover the *why*.
- Add the ADR to the README's decision log and update affected README rules in the same change.
