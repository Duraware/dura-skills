# Mode A: design workflow

Part of the `architect` skill. Loaded on demand from SKILL.md.

## A. Design workflow

Describe the architecture in four dimensions: **style + architecture characteristics + logical components + architecture decisions**. Work through them in this order, one step at a time, pausing at decision points. The result is documentation (README sections, ADRs, diagrams), not code.

### A1. Understand the domain and context
Ask only what is missing:
- Business problem, users/actors, main workflows; what makes money or keeps the company compliant
- Scale: users, concurrent sessions, requests/sec, data volume, growth, peaks/bursts
- Team: size, skills, number of teams, DevOps maturity (CI/CD, automated tests, observability); organisational factors (budget, M&A plans, vendor constraints)
- Constraints: deadline, cloud/on-prem, mandated tech, existing systems and data to integrate
- Legal: GDPR, sector rules, audit, data residency

### A2. Identify architecture characteristics
Architecture characteristics are the qualities the system needs besides its features (scalability, availability, security and so on). Treat a quality as *architectural* only when it shapes the structure of the system and matters for success. Example: basic security hygiene is just good design; strict tenant isolation that needs separate deployments is architectural.

Look for them in three places:
- **Stated**: written in the requirements (user counts, response times, uptime targets).
- **Unstated but expected**: nobody writes them down, but the system fails without them (availability, reliability, basic security), plus what the domain implies (data integrity for medical records, low latency for trading).
- **Translated from business goals**: ask what the business is worried about and turn it into qualities. Fast releases point to deployability and testability; growth through acquisitions points to interoperability and extensibility; a fixed budget points to simplicity.

Some qualities are **composites** that can't be measured directly. Break them into measurable parts: "agility" becomes how modular, how testable and how easy to deploy the system is; "the nightly batch must finish on time" involves performance, but also availability, recoverability and scalability.

Useful categories when brainstorming:
- **Operational**: availability, disaster recovery, performance, recoverability, reliability, robustness, scalability, elasticity
- **Structural**: configurability, extensibility, maintainability, portability, reuse, localization, upgradeability
- **Cross-cutting**: accessibility, auditability, authentication, authorization, legal, privacy, security, usability

Always make them measurable and pair performance with load ("p95 < 300 ms at 2,000 concurrent users"). Agree on shared definitions with stakeholders.

**Keep the list short:**
1. Collect up to about seven candidates; keep the expected-anyway ones in a separate list unless they need special structure.
2. When a stronger candidate appears, move the weaker one to an "also considered" list.
3. Ask which one they would give up if forced to.
4. Have stakeholders pick their **top 3, without ranking them**. Ranking a long list wastes time and rarely survives contact with reality.

Warn against trying to support every quality at once: each one adds complexity, and they pull against each other (more security usually costs some performance).

### A3. Scope: one set of characteristics or several?
An **architecture quantum** is the smallest part of the system that can be deployed on its own and has its own set of characteristics. Parts that share a database, or that call each other synchronously, end up in the same quantum: a slow synchronous dependency drags down the part that calls it, while an asynchronous queue absorbs the difference.

Name the kind of coupling you see: *semantic* (built into the problem), *implementation* (a choice we made), *static* (shared libraries, databases, contracts) or *dynamic* (calls at runtime). Tight coupling is fine inside a small scope; the wider the scope, the looser it should be.

Decision steps:
1. Does **one set of characteristics** fit the whole system? Then a **monolith** is the natural choice. If parts need clearly different qualities (a public site that must scale, a back office that must be secure and auditable), go **distributed** and draw the boundaries around those parts.
2. **Where does the data live?** One database, one per domain, or one per service.
3. **Sync or async?** Start with synchronous calls; switch to asynchronous only where you need the decoupling or buffering.

### A4. Choose the architecture style
Also weigh the domain, the data, cloud and data-transfer costs, the organisation and team maturity, and whether the *shape* of the problem matches the shape of the style (heavy per-customer customisation suits a plug-in core; tightly related multi-step forms suit coarse services better than many small ones).

**Partitioning:** *technical* (layers: presentation, business, persistence) or *domain* (by business capability, aligned with DDD). Remember **Conway's Law**: systems mirror the teams that build them, so domain-partitioned styles need domain-aligned teams, and you can reshape teams to get the structure you want.

**Distributed styles have hidden costs.** Whenever you recommend one, check the classic fallacies of distributed computing: the network is reliable, latency is zero, bandwidth is infinite, the network is secure, the topology never changes, there is one administrator, transport cost is zero, the network is homogeneous. Add the practical ones: versioning, compensating failed updates and observability are all harder than they look.

**Style overview** (rough guidance; every rating depends on how well the style is implemented):

| Style | Partitioning | Good at | Weak at | Use when | Avoid when |
|---|---|---|---|---|---|
| **Layered** | technical | low cost, simplicity | deployability, scalability, fault tolerance | small or simple apps, tight budget, unclear direction | large or growing systems; most requests just pass through layers unchanged |
| **Modular monolith** | domain | low cost, simplicity, clear modules | scalability, elasticity, independent deploys | new systems with unclear direction (extract modules later), domain-aligned teams | parts need very different scale or availability |
| **Pipeline** | technical | simplicity, composable steps | scalability, fault tolerance | one-way, ordered processing (ETL, integrations) | back-and-forth or unpredictable workflows |
| **Microkernel (plug-in)** | technical and domain | customisation, extensibility, testability | scalability, elasticity | products with per-customer or per-region variations | a core that keeps changing; plug-ins that depend on each other |
| **Service-based** | domain | independent deploys of a few coarse services, simple for a distributed style, local transactions | fine-grained scaling | a handful of business domains that must deploy independently; a shared database is acceptable | many services (more than roughly a dozen) or lots of calls between them |
| **Event-driven** | technical | responsiveness, scalability, fault tolerance, extensibility | simplicity, testability, workflow control, consistency | reacting to events, high throughput, parallel processing | mostly request/reply; flows that need strong consistency |
| **Space-based** | technical | extreme elasticity and scalability | cost, simplicity, testability, consistency | very spiky load (ticket sales, auctions) | large data volumes; strict consistency |
| **Orchestration-driven SOA** | technical | (historical) | deployability, testability, cost | mainly a lesson in over-centralised reuse | new systems |
| **Microservices** | domain | independent deploys, scalability, fault tolerance, evolvability | performance (network hops), cost, simplicity | many teams, mature DevOps, parts that change and scale independently | small teams, immature DevOps, workflows that need transactions everywhere |

Quick heuristics:
- Unclear direction, small team, tight budget: **modular monolith** (or layered for simple CRUD), built so modules can be pulled out later.
- A few coarse domains that must deploy independently: **service-based**, often the pragmatic middle ground. Don't pay for microservices you don't need.
- Fine-grained independent deploys: **microservices**. Event reactions: **event-driven**. Extreme spikes: **space-based**. Core plus customisations: **microkernel**. One-way transformations: **pipeline**.
- **Hybrids are normal** (service-based with an event-driven part, an API gateway in front). API gateway, CQRS and sidecar are *patterns*, not styles.
- Pure CRUD may not need much architecture at all; a framework or low-code tool can be enough.

Recommend **1 or 2 options**, each with: why it fits, how it serves the top 3 characteristics, what it sacrifices, cloud considerations, team fit, and what would make you change your mind.

### A5. Design logical components
A logical component is a building block implementing a business function, shown in code as a namespace or directory (leaf nodes are components; higher nodes are domains/subdomains). Create the **logical architecture** (components, actors, interactions) *before* the physical one (services, UIs, databases).

Iterative loop:
1. **Identify core components** as "empty buckets", using the **Workflow approach** (a component per major happy-path step) or the **Actor/Action approach** (the major actions per actor; *the system is always an actor too*).
   - Avoid the **Entity Trap**: CustomerManager, OrderManager. Suffixes like Manager, Supervisor, Controller, Handler, Engine and Processor are warning signs. Name components after their role (*Validate Order*, *Order Placement*).
2. **Assign user stories/requirements** to components. Add a new component rather than duplicating code across several.
3. **Analyse roles and responsibilities**: write a one-paragraph responsibility statement. Conjunctions (*and, also, in addition, as well as*) and long comma lists reveal a component doing too much; split it.
4. **Analyse architecture characteristics**: split components whose parts need different scalability, availability or agility.
5. **Restructure** continuously, with the developers.

Coupling and cohesion:
- **Cohesion**, best to worst: functional > sequential > communicational > procedural > temporal > logical > coincidental. LCOM measures structural lack of cohesion.
- **Afferent (Ca, fan-in)** and **efferent (Ce, fan-out)** coupling; **Abstractness** A, **Instability** I = Ce/(Ca+Ce), **Distance from the Main Sequence** D = |A + I − 1|. Avoid the *Zone of Pain* (concrete and stable, hard to change) and the *Zone of Uselessness* (abstract and unused).
- **Temporal coupling** (ordering/transactions) is hard to detect with tools; document it.
- **Law of Demeter** (principle of least knowledge): a component shouldn't know what other components must do; spread that knowledge to where it belongs.
- **Connascence** (Page-Jones):
  - *Static*: name < type < meaning (convention) < position < algorithm.
  - *Dynamic*: execution < timing < values < identity.
  - Prefer static over dynamic and refactor to weaker forms (magic value to named constant).
  - **Locality**: strong connascence is fine *inside* a module and harmful across modules or services.
  - Distributed transactions are connascence of values, the worst kind.

For each component or service also define: owned data, public contract, sync/async communication, and its quantum.

### A6. Data and communication patterns
- **Database topologies**: monolithic DB, domain databases, database-per-service. Align them with the style (microservices need DB-per-service; service-based is flexible).
- **Service-based data**: avoid a single shared entity library (any table change redeploys every service; an antipattern). Logically partition the DB with **one shared library per data domain** (plus a version-locked common one), as fine-grained as the data domains allow.
- **Database type** must amplify the style's superpowers. Scalability/elasticity: key-value, columnar. Relational data: relational. Heavy writes: columnar. Heavy reads: key-value, document, graph. Balanced: relational or NewSQL. Polyglot persistence where useful.
- **Orchestration vs choreography**:
  - Orchestration gives a centralised workflow, error handling, recoverability and state management, at the cost of responsiveness, a single point of failure, scalability and coupling.
  - Choreography gives responsiveness, scalability, fault tolerance and decoupling, at the cost of a distributed workflow and harder state, errors and recovery.
  - Choose per workflow; never as a blanket standard.
- **Transactions across services**: avoid them and fix the service boundaries instead. If unavoidable, use a **saga** with compensating transactions, and plan for the case where the compensation itself fails.
- **Microservice granularity**: decide from purpose (functional cohesion), transactions (entities in one transaction suggest one service) and choreography (too much chatter means merge). Avoid **Grains of Sand** (too fine-grained) and sharing code via libraries ("share nothing"; prefer duplication over coupling for domain code). Separate operational concerns with **sidecar / service mesh** (orthogonal coupling).
- **Events** (EDA):
  - Payload: *data-based* (performance, scalability) vs *key-based* (contract management, bandwidth, less stamp coupling).
  - Avoid **anemic events** (send before/after values) and the **Swarm of Gnats** (too many fine-grained derived events; emit one event per outcome or state change).
  - Handle async errors with the **Workflow Event pattern** (delegate, repair, resubmit; people handle the leftovers).
  - Prevent data loss with persisted queues, client-acknowledge mode and durable subscribers.
- **Queues vs topics**:
  - Topics: extensibility, decoupling, one message from the producer; but homogeneous contracts, weaker security, no per-consumer scaling.
  - Queues: heterogeneous contracts, security, independent monitoring and scaling; but more coupling and more infrastructure.
- **Shared library vs shared service**:
  - Library wins on versioning, change risk, performance, fault tolerance and scalability.
  - Service wins for heterogeneous platforms and highly volatile code.
  - Weight the criteria for the context.
- **CQRS** separates read and write stores to give them different characteristics. The **Single-Broker vs Domain-Broker** trade-off balances central discovery and minimal infrastructure against fault tolerance and throughput.

### A7. Deployment, release and migration
Recommend based on risk and team maturity: blue-green or deployment slots (instant rollback), canary, rolling, feature flags (decouple deploy from release; never for compliance controls), ring/staged rollout. Always cover DB migrations (expand/contract) and rollback compatibility. Document these as rules and plans; for migrations between styles, see mode M.

### A8. Governance: fitness functions
See mode F. Specify at least one fitness function per top characteristic.

### A9. Design output (as documentation)
Write these as README sections, ADRs and diagrams:
1. Context and assumptions
2. Architecture characteristics: top 3 (+ supporting, + others considered) and why; composite characteristics decomposed
3. Quanta and the monolith/distributed decision
4. Chosen style(s), with a trade-off table against the main alternative and the fallacies addressed
5. Logical components (responsibilities, data ownership, communication), then the physical view (C4 container diagram in Mermaid)
6. Data topology and key patterns
7. Deployment and release approach
8. Fitness functions (specified, for the team to implement)
9. Risks (risk matrix) and open questions
10. ADRs to write
