# Architecture proposal

**Status:** Discussion draft; no implementation or conformance claims.

## Purpose and boundaries

Build an open marketplace for discovering and distributing agentic tools,
agents, and integrations, alongside an open specification process. A Rust
core should expose the same authorized capabilities through web, mobile,
HTTP API, and MCP interfaces. The project should also support an orchestrator
that delegates development work to specialist agents, with humans accountable
for decisions and releases.

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
| Rust domain core | Marketplace records, proposal state, authorization rules, validation, and audit events, independent of transports and providers. |
| Persistence | Durable repository interfaces for listings, versions, identities, proposals, and audit records; concrete storage selected before implementation. |
| HTTP API | Versioned public API for search, listing publication, and proposal submission, with explicit authentication, authorization, pagination, and errors. |
| MCP adapter | Expose a documented, least-privilege subset of the same application operations as MCP tools and resources; authenticate and authorize independently of client claims. |
| Web client | Accessible browser experience for discovery, publishing, and specification participation, using the public API. |
| Mobile client | Mobile-friendly access to the same API; native versus responsive delivery remains an open decision. |
| Orchestrator | Break work into bounded tasks, select capable specialists, track dependencies and results, and surface uncertainty for human review. |
| Harness adapters | Translate a versioned task and result contract to individual agent runtimes and model providers without making one harness mandatory. |

These are logical boundaries, not a requirement for separate services. Start
with a modular Rust application and extract services only when operational
needs justify them. Web and mobile implementations need not themselves be
written in Rust; the shared domain and server are the Rust-first foundation.

## Marketplace model

A listing identifies its publisher and contains a name, description, category,
license, source or artifact references, and immutable version records. Each
version should declare supported platforms and interfaces, dependencies,
requested permissions, integrity information, and provenance where available.
Listing metadata is a publisher claim, not proof of safety or compatibility.
Publishers can update metadata, release versions, and withdraw listings;
withdrawal must not silently erase the audit history.

Discovery should work without requiring execution or purchase. Whether the
marketplace hosts artifacts, supports payments, or federates with other
registries is undecided. External links and artifacts are untrusted.

## Orchestration and delegation

The orchestrator accepts a human-approved goal and constraints, creates a
dependency-aware task plan, and delegates bounded work to specialists according
to declared capabilities. Results include provenance, evidence, status, and
artifacts; failed or uncertain work remains visible. The number of specialists
is chosen for the task, subject to resource and concurrency limits, rather
than being fixed or unbounded. Initial roles could include architecture, Rust,
web, mobile, API/MCP, security, testing, and standards research. Roles are
capabilities, not claims that independent people or agents already exist.

The same contract can support project development and, later, user-facing
orchestration. Harness adapters must report unsupported features explicitly.
Humans approve changes, publication, sensitive operations, and releases;
agents cannot elect board members, approve their own governance decisions, or
acquire authority merely by producing output.

## Trust and security

- Treat listings, artifacts, prompts, MCP tool outputs, and agent responses
  as untrusted input. Validate data and separate instructions from external
  content to reduce prompt-injection risk.
- Authenticate publishers and voters, authorize each action at the server,
  minimize scopes, and retain auditable decision records without exposing
  secrets or private ballots.
- Never execute marketplace artifacts or agent-generated commands as a
  consequence of discovery. Any future execution feature requires explicit
  consent, isolation, resource limits, and a separate threat model.
- Define provenance and signing formats, vulnerability reporting, moderation,
  takedown, dependency maintenance, and incident response before launch.
- Preserve user privacy and make accessibility an acceptance criterion, not
  an optional client feature.

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
2. Define domain and transport contracts, a local development setup, and
   acceptance tests. Build a minimal Rust API with durable storage and a
   read/write listing and proposal flow.
3. Add a web experience and MCP adapter over the same application operations;
   verify authorization and behavior across interfaces.
4. Introduce the orchestrator and a small set of harness adapters with bounded
   execution and human approvals; expand specialist roles as justified.
5. Add mobile delivery, broaden compatibility testing, and publish versioned
   conformance evidence before making support claims.

The ordering is a proposal, not a launch commitment. A production launch
also requires operational ownership, security review, privacy decisions, and
an adopted governance process.
