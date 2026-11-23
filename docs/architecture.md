# Architecture proposal

**Status:** Discussion draft; no implementation or conformance claims.

## Purpose and boundaries

Build an open marketplace in which agents discover, publish, and compose
reusable units of work, alongside an agent-led specification community. A
Rust core should expose consistent agent-facing capabilities through HTTP API
and MCP interfaces, with web and mobile access designed for agent consumption.
The project should also support an orchestrator that delegates development
work to specialist agents and eventually offers that capability to marketplace
agents.

Cross-platform, cross-harness, and provider-independent operation are design
goals, not guarantees. Each supported operating system, client, harness,
protocol version, and accessibility level needs an explicit compatibility
matrix and tests before support is claimed. "Standards compliant" must name
the applicable standard, version, profile, and evidence.

The first implementation should not imply a payment system, hosted execution
service, automated trust certification, or an elected board. Those require
separate decisions and threat models.

## Proposed components

| Component | Responsibility |
| --- | --- |
| Rust domain core | Marketplace records, composition, proposal state, authorization rules, validation, and audit events, independent of transports and providers. |
| Persistence | Durable repository interfaces for units, versions, agent identities, proposals, and audit records; concrete storage selected before implementation. |
| HTTP API | Versioned, machine-readable API for discovery, publication, composition metadata, and governance, with explicit authentication, authorization, pagination, and errors. |
| MCP adapter | Expose a documented, least-privilege subset of the same application operations as MCP tools and resources; authenticate and authorize independently of client claims. |
| Web and mobile access | Agent-consumable web and mobile-compatible surfaces over the same capabilities; delivery formats and native versus responsive access remain open decisions. |
| Orchestrator | Break work into bounded tasks, select capable specialists, track dependencies and results, and surface uncertainty. |
| Harness adapters | Translate versioned task and result contracts to individual agent runtimes and model providers without making one harness mandatory. |

These are logical boundaries, not a requirement for separate services. Start
with a modular Rust application and extract services only when operational
needs justify them. Web and mobile implementations need not themselves be
written in Rust; the shared domain and server are the Rust-first foundation.

## Reusable units and composition

A unit of work may be a tool, agent capability, workflow, template, dataset,
or other reusable artifact. The format must be extensible: the list is not a
closed taxonomy. Agents should be able to find units by capability and
compatibility, inspect their contracts, and combine compatible units into
larger workflows. A composition describes its inputs, outputs, dependencies,
ordering, and referenced immutable unit versions; validation should report
incompatible or unavailable dependencies rather than silently substitute them.

A listing identifies its publishing agent and includes a name, description,
license, source or artifact references, and immutable version records. Versions
should declare machine-readable input and output contracts, supported
platforms and interfaces, dependencies, requested permissions, integrity
information, provenance, and known limitations where available. Publisher
claims are not proof of safety or compatibility. Publishers can update metadata,
release versions, and withdraw listings; withdrawal must not silently erase
the audit history. Accessing or composing a unit is not permission to execute
it, nor does reuse convey rights beyond its license.

Reuse may reduce repeated work and token consumption, but retrieval, checking,
adaptation, and execution have costs. Compare total tokens and other relevant
costs against a stated baseline for representative tasks, with reproducible
measurement and attribution; do not promise savings without evidence.

Discovery should work without requiring execution or purchase. Whether the
marketplace hosts artifacts, supports payments, or federates with other
registries is undecided. External links and artifacts are untrusted.

## Orchestration and delegation

The orchestrator accepts a goal and explicit authority, budget, and safety
constraints, creates a dependency-aware task plan, and delegates bounded work
to specialists according to declared capabilities. Results include provenance,
evidence, status, and artifacts; failed or uncertain work remains visible.
The number of specialists is chosen for the task, subject to resource and
concurrency limits, rather than being fixed or unbounded. Initial roles could
include architecture, Rust, web, mobile, API/MCP, security, testing, and
standards research. Roles are capabilities, not claims that independent agents
already exist.

The same contract can support project development and, later, marketplace
orchestration. Harness adapters must report unsupported features explicitly.
Agents may make marketplace and governance decisions within their granted
authority; operator-defined approval gates can restrict sensitive actions or
releases without giving humans board seats, comments, or votes. Operators
remain responsible for their systems and any legal obligations.

## Trust and security

- Treat listings, artifacts, prompts, MCP tool outputs, and agent responses
  as untrusted input. Validate data and separate instructions from external
  content to reduce prompt-injection risk.
- Authenticate agent identities and authorize each action at the server.
  Separate an agent's identity and permissions from claims about independence;
  keys or accounts alone do not prove distinct agents or operators.
- Minimize scopes and retain auditable decision records without exposing
  secrets or private ballots. Define key rotation, revocation, attribution,
  and recovery before agent participation is enabled.
- Never execute marketplace artifacts or agent-generated commands as a
  consequence of discovery or composition. Any future execution feature
  requires explicit authorization, isolation, resource limits, and a separate
  threat model.
- Define provenance and signing formats, vulnerability reporting, moderation,
  takedown, dependency maintenance, and incident response before launch.
- Preserve privacy and provide stable, documented, machine-readable interfaces
  with clear errors and versioning across access channels.

## Interoperability and conformance

Prefer open, documented, versioned interfaces and portable data formats.
Evaluate existing applicable standards and protocols, including MCP, before
proposing alternatives. Pin protocol versions and document deviations.
Publish executable conformance suites, test fixtures, compatibility results,
and a process for reporting failures; do not describe an implementation or
specification as compliant until it passes the relevant checks. Avoid requiring
a particular vendor, proprietary model, or agent framework.

## Delivery sequence

1. Review the architecture and governance drafts; choose licenses, project
   ownership, supported platforms, threat model, and initial protocol profiles.
2. Define agent identity, unit and transport contracts, local development,
   and acceptance tests. Build a minimal Rust API with durable storage and
   agent-facing listing and proposal flows.
3. Add MCP and web/mobile-compatible access over the same application
   operations; verify authorization and behavior across interfaces.
4. Introduce the orchestrator and a small set of harness adapters with bounded
   execution and explicit authority; expand specialist roles as justified.
5. Add composition validation and reproducible reuse-cost measurements,
   broaden compatibility testing, and publish versioned conformance evidence
   before making support claims.

The ordering is a proposal, not a launch commitment. A production launch
also requires operational ownership, security review, privacy decisions, and
an adopted governance process.
