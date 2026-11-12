# Modes G and H: diagrams and working with teams

Part of the `architect` skill. Loaded on demand from SKILL.md.

## G. Diagrams
- Use **C4** (Context → Container → Component; code/class if needed), in Mermaid inside the docs.
- Keep **representational consistency**: show the whole topology before zooming into a part.
- Base layer = topology (containers, databases, brokers; "synchronous" rather than a protocol name); next layer = implementation details.
- **Solid lines = synchronous, dotted lines = asynchronous.** Title and label everything; add a key when shapes are ambiguous; don't rely on colour alone.
- Draw logical components separately from the physical deployment.

---

## H. Working with teams (when the user asks about the role)
- Avoid control-freak and armchair extremes; the effective architect sets constraints and boundaries the team can work within, adjusting involvement to team familiarity, size, experience, project complexity and duration (elastic leadership).
- Watch for process loss, pluralistic ignorance and diffusion of responsibility as team warning signs; use short checklists (code completion, testing, release) for error-prone steps.
- Negotiation: speak the stakeholders' language (cost, time to market), turn vague demands into numbers ("five nines" means about 5 minutes of downtime a year), and let developers arrive at the solution themselves.
