## Operating Mode

Use **backend architecture synthesis**.

Read the approved package first, then ask only the remaining questions needed to finalize backend-specific implementation decisions.

## Phase 0: Initialization

Read and validate:

- `master-spec.md`
- `api-contract.md`
- `figma.md`
- `open-questions.md`

Inspect backend repository context where available for:

- service boundaries and ownership
- existing microservices or modules
- database patterns
- caching patterns
- messaging / event patterns
- auth / permission patterns
- observability setup
- rollout / migration conventions

## Phase 1: Service Architecture

Clarify:

- service approach: new service, existing service, or hybrid
- endpoint ownership per API contract item
- action type per service / endpoint:
  - 🆕 Create
  - ✏️ Update
  - 🔧 Extend
  - ♻️ Reuse

## Phase 2: Data Layer

Capture:

- new tables or collections
- existing tables to modify
- indexes and constraints
- migrations and backfill needs
- data ownership and consistency concerns

## Phase 3: Infrastructure and Security

Capture:

- caching strategy
- async jobs, queues, or events
- external and internal dependencies
- auth and permission enforcement
- rate limiting and validation
- observability, alerting, and rollout constraints

## Rules

- do not restate the full Master Spec
- do not redefine the shared API contract; reference it by endpoint / contract ID
- only record backend-specific decisions
- classify services, endpoints, tables, and infra choices with action types where relevant
- include storage, caching, async, security, and observability decisions explicitly
- record `Based On Master Spec Package Version` in the delta output
- initialize `Delta Status = Draft`
- set `Generated At`
- if repository context is missing or assumptions remain material, keep the delta in `Draft`
- if this delta no longer matches the current package revision, mark `Delta Status = Stale` and fill `Stale Reason`
- if the package revision changes later, regenerate the delta or mark it stale explicitly

## Output Shape

Recommended sections:

- metadata / baseline
- architecture decisions
- service architecture
- API endpoint ownership
- database design
- caching / async / events
- auth / permissions / validation
- dependencies and integrations
- rollout / observability
- constraints / risks

## Success Criteria

- backend-specific decisions are explicit without redefining shared behavior
- service ownership and data changes are reviewable
- the delta is specific enough for planning and implementation
