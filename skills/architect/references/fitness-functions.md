# Mode F: governance and fitness functions

Part of the `architect` skill. Loaded on demand from SKILL.md.

## F. Governance and fitness functions
A **fitness function** is *any mechanism that gives an objective integrity assessment of an architecture characteristic*: unit-test libraries, metrics, monitors, chaos engineering. Treat fitness functions like a checklist, not heavy bureaucracy, and make sure developers understand the purpose of each one before imposing it. You **specify** fitness functions in the documentation (with example snippets); the team adds them to the codebase.

Measures:
- *Operational*: response time with percentiles and maxima, not just averages; statistical baselines; performance budgets
- *Structural*: cyclomatic complexity (industry threshold < 10, the authors prefer < 5), cycles, coupling metrics
- *Process*: coverage (watch for tests without assertions), deployment success rate and duration, bugs per deploy

Examples to propose:
- No cyclic dependencies; layer and module access rules; "module X may not reference module Y"; every namespace belongs to a defined module; a maximum number of inter-module dependencies (ArchUnit, ArchUnitNET or NetArchTest for .NET, PyTestArch, TSArch, dependency-cruiser)
- Distance from the Main Sequence threshold
- Every test has at least one assertion
- Style-specific checks:
  - Microkernel: churn/volatility of the core (from version control), contract tests
  - Service-based: changes shouldn't span several services; limit inter-service calls
  - EDA: rate of contract change, unused event fields (stamp coupling), logged synchronous calls between processors
  - Microservices: shared-artifact count (SBOM), inter-service call map from logs or the registry
  - Space-based: cache memory usage
- Operational: latency/SLO alerts, load tests in CI, chaos tests (Chaos Monkey-style), security conformity checks, orphaned-service detection

Specify for each: what it guards, tool, threshold, and where it runs (CI build, deploy pipeline, production monitor).
