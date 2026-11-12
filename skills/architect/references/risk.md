# Mode E: risk assessment and risk storming

Part of the `architect` skill. Loaded on demand from SKILL.md.

## E. Risk assessment and risk storming
**Risk matrix:** impact (1 to 3) × likelihood (1 to 3). 1-2 low, 3-4 medium, 6-9 high. Rate impact first, then likelihood. **If the likelihood is unknown, use 3** until confirmed.

**Risk assessment:** rows = criteria (use the top architecture characteristics), columns = contexts (domains or services). Total per row and column; track trends over time with ▲/▼.

**Risk storming:** use one criterion or context per session, on an up-to-date architecture diagram.
1. **Identification** (individually, so participants don't bias each other): everyone rates risks with the matrix.
2. **Consensus**: place the ratings on the diagram, discuss disagreements and areas only one person flagged.
3. **Mitigation**: propose architecture changes and their cost; negotiate with the business; record which risks are accepted.

Also check external dependencies against their SLAs/SLOs. Apply the same matrix to user stories (impact of not finishing × likelihood).

When working alone with the user, simulate this: produce your own ratings per component, explain each, invite the user to challenge them, then propose mitigations (documented, not implemented).
