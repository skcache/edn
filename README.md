<div align="center">

# `edn.`

## Engineering Dashboard Notebook

### **AI writes the code. You still own the system.**

A tiny Agent Skill that keeps a clean, local engineering notebook synchronized with the code your coding agent builds.

</div>

---

## The problem

Coding agents are very good at turning a vague feature request into a surprising amount of working software.

That creates a new failure mode:

```text
you describe the goal
        ↓
agent chooses the architecture
        ↓
agent writes the implementation
        ↓
tests pass
        ↓
you own a codebase you cannot fully explain
```

`edn.` moves your attention back to the system.

```text
goal
  ↓
current architecture
  ↓
architecture check when the change is structural
  ↓
your decision
  ↓
agent implementation
  ↓
verification
  ↓
engineering notebook updates
```

The agent still handles implementation throughput.

You retain control of:

- architecture
- component boundaries
- dependencies
- data/state ownership
- system flows
- tradeoffs
- failure modes
- security and tenant boundaries
- meaningful technical decisions

---

## What gets created

By default, one local file:

```text
engineering-notebook.html
```

The skill automatically adds it to `.gitignore`.

It stays local unless **you explicitly decide to commit it**.

The notebook covers:

```text
Project Map
System Architecture
Core Components
Key Flows
Data / State Ownership
Decisions & Tradeoffs
Failure Modes / Risks
Current vs Proposed Architecture
Recent Architectural Changes
Dependencies & External Contracts
Verification & Evidence
```

Think clean developer documentation, not another control panel full of buttons.

---

## It adapts to your level

On first use, `edn.` asks how technical you want the notebook/explanations to be:

```text
Intern
New Grad
Junior
Mid-Level
Senior
Staff
Principal
Distinguished
```

This does **not** change the rigor.

It changes how much context the agent provides.

A junior engineer might get:

```text
Redis would add an in-memory cache between ProductService and PostgreSQL.

Why it might help:
Repeated reads could avoid the database.

What it costs:
Cache invalidation, possible stale data, and another runtime dependency.

But we haven't shown the database is the bottleneck yet.
Measure the query first.
```

A staff-level view can compress that into the actual system tradeoff.

No “humans need to understand this” language. No condescension. Just calibrated engineering context.

---

## Architecture checks only happen when they matter

Routine work should stay fast.

No ceremony for:

- CSS
- validation
- tests
- small bug fixes
- internal helpers
- implementation details behind existing interfaces

The agent pauses before changes that alter how you need to mentally model the system:

- new database/store
- cache
- queue
- service/process
- external API/dependency
- auth/security boundary
- tenant isolation
- major schema redesign
- state ownership
- consistency model
- concurrency/async model
- subsystem interface
- deployment topology

The check includes:

```text
current architecture
proposed architecture
why it may help
tradeoffs
evidence
simpler alternatives
concepts worth knowing
recommendation
decision
```

---

## Evidence > cargo culting

`edn.` explicitly tells the agent to prefer:

- measurements
- profiling
- benchmarks
- tests
- traces
- official documentation
- reproducible evidence

over:

- “best practice” with no context
- tutorial architecture
- scale assumptions nobody measured
- adding Redis because somebody on YouTube said caching is production-grade

If the agent proposes complexity for performance, it should first show what is actually slow.

---

## Security boundaries are first-class

For auth, tenant data, shared caches, background jobs, and ownership-sensitive changes, the notebook asks:

```text
Who can access this?
Where is access enforced?
What identifier scopes the data?
What is the ownership source of truth?
Could one tenant/user touch another's data?
What tests prove isolation?
```

Because “tests passed” is a pretty weak victory lap if Tenant A can read Tenant B's invoice.

---

## Clean visual notebook

The HTML notebook should look like high-quality modern developer documentation:

- readable at normal laptop dimensions
- clean navigation
- restrained styling
- good typography
- minimal visual noise
- system diagrams that actually fit
- solid edges for current architecture
- dotted/dashed edges for proposed architecture
- readable labels
- no clipped nodes
- no diagram spaghetti

The skill explicitly tells the agent to **inspect generated diagrams instead of assuming the first attempt is good**.

A revolutionary concept, apparently.

---

## Install

Agent Skills are directories containing a `SKILL.md` with `name` and `description`.

Once this repository is public:

```bash
npx skills add YOUR_GITHUB_USERNAME/engineering-dashboard-notebook
```

Install to a specific supported agent if desired:

```bash
npx skills add YOUR_GITHUB_USERNAME/engineering-dashboard-notebook -a codex
```

or:

```bash
npx skills add YOUR_GITHUB_USERNAME/engineering-dashboard-notebook -a claude-code
```

The open `skills` CLI supports Codex, Claude Code, Cursor, OpenCode, Copilot, and many other agents.

---

## Start using it

Tell your agent:

```text
Use the engineering-dashboard-notebook skill.
Bootstrap the local notebook for this repository before we continue.
```

The first run asks your preferred engineering explanation level and generates the current system view.

Then just build.

---

## Example

You:

```text
We keep recomputing these product summaries. Add caching.
```

Without an architecture-owner workflow:

```text
Sure.
```

Three minutes later Redis has apparently joined the company.

With `edn.`:

```text
Architecture Check

Current:
ProductService → PostgreSQL

Proposed:
ProductService → Redis
               → PostgreSQL

Potential gain:
avoid repeated expensive reads

Evidence:
we have not measured the query yet

New complexity:
stale data
cache invalidation
new runtime dependency

Simpler alternative:
profile/index the existing query first

Decision:
measure first, or accept the cache complexity now?
```

You decide.

The agent types.

---

## What this is not

`edn.` is not:

- a coding agent
- an autonomous architect
- a project-management framework
- a replacement for Spec Kit / OpenSpec
- an interactive “click to implement” dashboard
- a reason to never inspect code
- another SaaS
- a 40-file methodology

V0 is deliberately three files:

```text
engineering-dashboard-notebook/
├── SKILL.md
├── README.md
└── LICENSE
```

---

## Why this exists

Generated code makes implementation cheaper.

It does not make:

- system design
- judgment
- security
- tradeoffs
- debugging
- performance reasoning
- ownership

cheap.

The goal is not to prove that you personally typed every line.

The goal is to be able to defend why the system is shaped the way it is.

---

## Compatibility

The skill follows the open Agent Skills `SKILL.md` format.

The `skills` CLI can install public skills from GitHub and currently supports a broad set of coding agents.

---

## License

MIT.
