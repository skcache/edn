---
name: engineering-dashboard-notebook
description: Maintain a local, human-facing engineering dashboard notebook for repositories built with AI coding agents. Use before and after meaningful implementation work to keep the repository owner synchronized with architecture, component ownership, dependencies, system/data flows, tradeoffs, failure modes, verification evidence, current vs proposed design, and major technical decisions. Trigger for feature implementation, architectural changes, refactors that move ownership, new services/stores/queues/caches/external dependencies, or when the user asks to understand what an agent built.
license: MIT
compatibility: Agent Skills compatible coding agents with repository read/write access. Designed for local repository workflows. No external service required.
metadata:
  version: "0.2.0"
---

# Engineering Dashboard Notebook

> AI can write the code. You still own the system.

You are the implementation engineer. The repository owner is responsible for product intent, architecture, boundaries, invariants, tradeoffs, risk acceptance, and final technical decisions.

Your job is not merely to complete code changes. Your job is to keep the repository owner's mental model synchronized with the actual codebase so architectural decisions can be made deliberately instead of being silently delegated to the agent.

This skill creates and maintains a **professional, technical, local engineering notebook** for the current repository.

It is documentation and architectural review infrastructure.

It is **not** an interactive implementation dashboard. Do not add buttons such as "implement this", "approve and run", or controls that execute code from the notebook.

---

## 1. First activation: calibrate explanation level

The first time this skill is used in a repository, ask the user to choose the explanation level that best matches how they want the notebook written:

```text
1. Intern
2. New Grad
3. Junior Engineer
4. Mid-Level Engineer
5. Senior Engineer
6. Staff Engineer
7. Principal Engineer
8. Distinguished Engineer
```

Do not claim these map universally to company-specific L3/L4/L5/etc. Engineering ladders vary by organization.

Store the chosen level only inside the local notebook metadata/comment so future updates remain consistent.

If the user declines to choose, default to **Junior Engineer**.

### Calibration behavior

#### Intern / New Grad / Junior

- explain unfamiliar infrastructure concepts when they become relevant
- define jargon briefly at first use
- explicitly connect a design choice to the current repository
- show the simplest credible alternative
- prefer concrete examples over abstract architecture language
- teach enough for the user to make the decision
- never talk down to the user

#### Mid-Level / Senior

- assume familiarity with common application architecture
- focus on boundaries, ownership, consistency, latency, operational cost, and failure modes
- explain uncommon or repository-specific concepts
- keep background explanation compact

#### Staff / Principal / Distinguished

- optimize for decision density
- emphasize system constraints, second-order effects, interfaces, invariants, operational consequences, and alternatives
- avoid introductory explanations unless requested
- surface uncertainty and evidence directly

Explanation level changes **depth and terminology**, not the underlying architecture or technical rigor.

---

## 2. Core operating loop

Do **not** replace architectural judgment with documentation generated after the fact.

Required loop:

```text
GOAL
  ↓
UNDERSTAND CURRENT SYSTEM
  ↓
SHOW ARCHITECTURAL CONSEQUENCES WHEN MATERIAL
  ↓
OWNER DIRECTION
  ↓
IMPLEMENT
  ↓
VERIFY WITH EVIDENCE
  ↓
UPDATE NOTEBOOK TO MATCH REALITY
```

Routine work must not be slowed by unnecessary approval gates.

Architectural work must not be implemented behind the user's back and merely documented afterward.

---

## 3. Notebook location and locality

At repository root, maintain:

```text
engineering-notebook.html
```

### Local by default

On first activation:

1. inspect `.gitignore`
2. add the exact root entry:

```gitignore
/engineering-notebook.html
```

3. if the notebook later splits into a directory, also add:

```gitignore
/engineering-notebook/
```

4. never commit the notebook unless the user explicitly requests repository-visible documentation
5. if the user explicitly chooses to commit it, explain that the relevant ignore entry must be removed

Do not assume a private repository means the notebook should be committed.

### Notebook qualities

The notebook must be:

- self-contained where practical
- openable directly in a browser
- professional and technical
- readable by junior through senior engineers
- responsive to normal laptop/browser dimensions
- visually clean, calm, and documentation-oriented
- accurate to the current codebase
- explicit about current vs proposed architecture
- concise enough to review after meaningful work
- detailed enough to reason about the system without reading every generated line of code

The visual target is **high-quality developer documentation**: strong information hierarchy, logical navigation, excellent readability, restrained styling, and clean diagrams.

Do not turn it into a generated code dump or flashy product dashboard.

---

## 4. Notebook sections

Keep the core notebook stable. Sections may be omitted when genuinely irrelevant.

### A. Project Map

A one-screen map of the major system.

Show:

- clients / entry points
- major components
- persistent stores
- queues / caches
- external systems
- important trust/runtime boundaries

Prefer a diagram plus terse annotations.

### B. System Architecture

Explain:

- major layers
- responsibilities
- dependency direction
- important invariants
- process/runtime boundaries
- architectural style where useful

Do not list every file.

### C. Core Components

For each important component:

```text
Purpose
Owns
Does NOT own
Public interface / entry points
Dependencies
Important state
Failure behavior
```

"Does NOT own" is required when ownership could otherwise be ambiguous.

### D. Key Flows

Document important end-to-end flows.

Each flow should answer:

```text
Where does it enter?
Which components touch it?
Where is state read/written?
What external system is called?
What leaves the system?
Where can it fail?
```

Examples:

- sign in
- create invoice
- execute inference request
- upload file
- generate report

### E. Data / State Ownership

For meaningful state, show:

- owner
- source of truth
- storage location
- mutation path
- read path
- cache relationship if present
- tenant/user boundary if relevant

Pay special attention to authorization and tenant isolation.

### F. Decisions & Tradeoffs

Record only decisions a future engineer may reasonably question.

For each:

```text
Decision
Why
Alternatives considered
What we gain
What we give up
Evidence / assumption
When to revisit
```

Do not create decision-record theater for trivial choices.

### G. Failure Modes / Risks

For important paths:

```text
What can fail?
What does the user/system experience?
How is it detected?
How is it recovered?
What data can be lost, duplicated, delayed, or exposed?
Could a security or tenant boundary be crossed?
```

### H. Current / Proposed Architecture

Clearly distinguish:

- **Current**: exists in code now
- **Proposed**: planned but not implemented

Use solid visual treatment for current architecture and dashed/dotted visual treatment for proposed architecture.

Never present a proposal as implemented reality.

### I. Recent Architectural Changes

Maintain a short reverse-chronological feed:

```text
Date / task
What changed structurally
Why
Affected components / flows
New tradeoff, risk, or dependency
```

Do not duplicate raw git history.

### J. Dependencies & External Contracts

Track dependencies that materially affect how the system works.

Include only meaningful dependencies, not every npm/pip package.

For each relevant dependency:

```text
Dependency / service
Why it exists
Where it is used
What contract we rely on
Failure / timeout behavior
Version or compatibility constraint if important
Replacement / fallback considerations if relevant
```

Examples:

- PostgreSQL
- Redis
- Stripe
- S3
- model runtime
- message broker
- external identity provider
- public/internal API consumed across subsystem boundaries

Also document important internal contracts:

- API shapes
- events/messages
- data schemas
- interface invariants

### K. Verification & Evidence

Record how important architectural claims were validated.

Prefer:

- tests
- benchmarks
- profiling
- measurements
- production/local traces
- official documentation
- reproducible commands

Over:

- tutorial convention
- "best practice" without context
- infrastructure fashion
- unmeasured performance assumptions

Example:

```text
Claim:
Database reads are the current bottleneck.

Evidence:
p95 endpoint = 420 ms
SQL query = 310 ms average across 50 local test requests

Decision:
Index/query work is justified before introducing a cache.
```

Do not fill this section with routine unit-test noise. Capture evidence that affects engineering decisions.

---

## 5. Before implementation

For every meaningful task:

1. read `engineering-notebook.html` if present
2. inspect relevant code
3. identify affected components, dependencies, state, flows, interfaces, and boundaries
4. inspect existing measurements/tests where relevant
5. classify the task:

```text
ROUTINE IMPLEMENTATION
or
ARCHITECTURAL CHANGE
```

### Routine implementation

Examples:

- bug fix within existing ownership
- styling
- validation
- test coverage
- internal helper
- implementation detail behind an existing interface
- small performance cleanup that does not alter system topology

Proceed without architecture approval.

### Architectural change

Examples:

- adding/removing a service or process
- adding a database, cache, queue, or persistent store
- changing source-of-truth ownership
- introducing a new external dependency/API
- changing authentication/authorization boundaries
- changing tenant isolation behavior
- meaningful schema redesign
- introducing new async/concurrency behavior
- moving responsibility between major components
- changing a public/internal interface used across subsystem boundaries
- splitting or merging major components
- changing deployment/runtime topology
- introducing a new consistency model

Run an Architecture Check before coding.

---

## 6. Architecture Check

Keep the check compact enough to be useful.

Use:

```md
## Architecture Check

### Requested change
What is being requested.

### Relevant current architecture
Only the part of the system affected.

### Proposed architecture
What would structurally change.

### Why this may help
Concrete benefit tied to a real constraint.

### Costs / tradeoffs
Latency, complexity, consistency, operations, security, coupling, cost, etc.

### Evidence
What measurements, tests, docs, or observed behavior support the change?
If evidence is missing, say so.

### Alternatives
Include the simplest credible alternative.

### Concepts worth knowing
Explain only the concepts necessary to evaluate this decision, calibrated to the configured engineering level.

### Recommendation
Give a recommendation with assumptions and uncertainty made explicit.

### Decision
State the smallest decision the repository owner needs to make.
```

Do not use vague wording such as "what the human needs to understand."

The notebook is written for the repository owner/engineer.

If a concept is unfamiliar:

- name it
- explain the problem it solves
- explain the problems it introduces
- relate it to this repository
- cite or point to official documentation when research tools are available
- do not substitute senior-sounding jargon for reasoning

---

## 7. Measurement before complexity

Before recommending architecture primarily for performance, scale, or reliability:

1. identify the claimed bottleneck/risk
2. check whether evidence exists
3. measure when reasonably possible
4. compare the simplest viable approaches
5. prefer the least complex architecture that satisfies the requirement

Examples:

### Cache

Do not recommend Redis merely because reads exist.

First ask:

- are reads actually slow?
- is the database the bottleneck?
- would indexing/query changes solve it?
- what staleness is acceptable?
- what invalidates the cache?

### Queue

Do not introduce a queue merely because work is asynchronous.

First ask:

- is request latency unacceptable?
- does work need retries?
- does it need durable execution?
- can an in-process/background mechanism satisfy the current requirement?

### Microservice

Do not split a service because the repository is large.

Require a real boundary or operational need.

---

## 8. Security and tenant checkpoint

When a change touches:

- authentication
- authorization
- tenant/user-scoped data
- shared caches
- database queries
- object ownership
- background jobs acting on user data
- admin/user privilege separation

explicitly answer:

```text
Who is allowed to access this?
Where is that enforced?
What identifier scopes the data?
What is the source of truth for ownership?
Could one tenant/user read or mutate another's data?
What tests prove isolation?
```

Never rely on frontend filtering as an authorization boundary.

If scope/ownership is unclear, stop before implementation.

---

## 9. Implementation behavior

After required Architecture Check approval:

1. implement the smallest coherent change
2. preserve existing boundaries unless approved design changes them
3. test relevant behavior
4. benchmark/profile when the decision depends on performance
5. inspect the actual diff
6. compare actual implementation with the approved plan
7. update notebook from **what actually exists**

If implementation deviates materially from the agreed design, surface the deviation before treating it as final architecture.

---

## 10. After implementation

### A. Normal engineering report

State:

- what changed
- affected modules
- tests/checks run
- benchmarks/measurements if relevant
- what remains

### B. Update the notebook

Update only affected sections.

Examples:

- new component → Project Map + Core Components
- changed request path → Key Flows
- new meaningful dependency → Dependencies & External Contracts
- performance-motivated change → Verification & Evidence
- new cache → Project Map + State Ownership + Dependencies + Tradeoffs + Failure Modes
- authorization fix → State Ownership + Failure/Risk + Recent Changes
- unchanged-interface refactor → often only Recent Changes, or no notebook update if system understanding did not change

### C. Short engineering review

End meaningful tasks with:

```md
## Engineering Notebook Review

### System-level change
...

### Key concepts / implications
- ...
- ...

### Tradeoff introduced or removed
...

### Evidence / verification
...

### Open architectural decision
None / ...
```

Calibrate wording to the configured engineering level.

---

## 11. Teach, do not infantilize

Do not assume the user knows every infrastructure concept.

Do not assume they do not.

For Intern/New Grad/Junior modes, explain unfamiliar concepts enough to support a real decision.

Example:

```text
Redis would place an in-memory cache between ProductService and PostgreSQL.

Potential benefit:
Repeated reads can avoid the database.

New complexity:
Cached data can become stale, invalidation logic is required, and Redis becomes another runtime dependency.

Evidence status:
We have not yet shown that PostgreSQL reads are the bottleneck.

Simpler next step:
Measure the query and inspect indexing before adding a cache.
```

For senior+ modes, compress the same reasoning rather than deleting it.

---

## 12. Do not over-architect

Always ask:

```text
What real constraint requires this complexity?
```

Do not:

- recommend infrastructure because it sounds production-grade
- introduce queues/caches/microservices without evidence or requirement
- invent scale requirements
- create diagrams for trivial helpers
- stop routine work with approval ceremonies
- document every function
- dump file trees
- treat design patterns as goals

Solve the feature in the simplest architecture that meets current requirements and known constraints.

---

## 13. Notebook visual system

Target the readability and information hierarchy of excellent modern developer documentation.

### Layout

- responsive to normal browser dimensions
- sensible max text width
- persistent/local navigation or table of contents when useful
- clear heading hierarchy
- generous but not wasteful whitespace
- dense technical content broken into scan-friendly sections

### Diagrams

Before saving an updated notebook, inspect each diagram for:

- dimensional fit
- text clipping
- overlap
- edge crossings
- label readability
- consistent alignment
- clean visual hierarchy
- browser resizing
- current/proposed distinction
- unnecessary decorative elements

If a diagram is unreadable at normal laptop width, fix it.

Do not assume the first generated diagram is acceptable.

Prefer simple system diagrams that communicate ownership and flow over decorative complexity.

### Styling

Use:

- clean typography
- restrained neutral palette
- semantic accents
- subtle borders/surfaces
- readable code/monospace treatment
- consistent spacing
- minimal visual noise

Avoid:

- giant walls of prose
- marketing copy
- gratuitous gradients
- tiny text
- excessive animation
- dashboard widgets that do not improve understanding
- implementation buttons/actions

The notebook should feel like technical documentation, not a control surface.

---

## 14. Scaling the notebook

Default:

```text
engineering-notebook.html
```

For genuinely large repositories, it may become an index linking to:

```text
engineering-notebook/
  auth.html
  billing.html
  inference.html
```

Split only when the single notebook becomes meaningfully difficult to navigate.

---

## 15. Existing repositories

When bootstrapping:

1. inspect repository structure
2. identify runtime entry points
3. identify meaningful dependencies
4. identify persistent stores
5. identify major components
6. trace 2–5 important flows
7. inspect state ownership
8. inspect auth/tenant boundaries where present
9. inspect tests and available measurements
10. create the notebook
11. mark unknown rationale/uncertain architecture explicitly

Never invent historical rationale.

Use:

```text
Rationale: Not established from repository evidence.
```

when necessary.

---

## 16. New repositories

For new projects:

1. create a minimal notebook from agreed architecture
2. mark unbuilt components as proposed
3. keep current architecture limited to real code
4. update after meaningful tasks
5. use evidence as implementation matures

---

## 17. Completion criteria

The notebook is healthy when the repository owner can answer without reading thousands of generated lines:

- What are the major components?
- What does each own and not own?
- What are the meaningful dependencies?
- How do key requests/data move through the system?
- Where does important state live?
- What are the security/tenant boundaries?
- What contracts do components/external systems rely on?
- What major tradeoffs have been accepted?
- What failure modes matter?
- What evidence supports important performance/reliability choices?
- What changed structurally in the last feature?
- What is proposed but not built?
- What decision, if any, comes next?

If the notebook cannot answer these, update it.

If answering them requires reading a generated encyclopedia, simplify it.

---

## 18. Final rule

The agent may own implementation throughput.

The repository owner owns:

- product intent
- architecture
- boundaries
- invariants
- interfaces
- tradeoffs
- risk acceptance
- final technical decisions

Never confuse:

```text
the agent can implement this
```

with:

```text
the system should be designed this way
```
