# API-CONTRACT-{FEATURE_ID}: {Feature Name}

> Extends: `MS-CORE-{FEATURE_ID}`
> Artifact: Shared API Contract
> Owner: Product / Backend / Client Leads
> Master Spec Package Version: `rX`
> Package Status: Draft / Approved

This artifact is the human-readable contract decision log for the current package baseline.

Use it to answer these questions quickly:

- Is any new backend surface required?
- Which existing APIs or events are confirmed for reuse?
- Which items are still pending product or backend decisions?
- Which unresolved items are blocking the shared baseline?

Render a standalone telemetry section only when the story has at least one real telemetry item to track:

- `Ready`
- `Waiting for event`
- `Reuse existing`
- `Pending decision`

If every telemetry note is effectively `Not needed`, keep that conclusion in the summary or decision matrix and omit the standalone telemetry section entirely.

Do not force every item into a fake request or response schema.

Default status rule:

- If a business or telemetry need is clearly in scope but no backend source of truth is bound yet, default to `New endpoint required`.
- Only use `Pending decision` when the team has not yet decided whether a standalone contract item is needed at all.
- For telemetry, prefer a lightweight event register. Do not force ECS payload-field tables into this artifact unless the event schema itself is the decision under review.

- `Confirmed reuse`: point to the existing endpoint, API doc, or source link and list only the consumed fields.
- `Pending decision`: point to the `OQ-*` item that must be resolved and state `No standalone request shape confirmed yet.`
- `New endpoint required`: draft the request and response schema inline.
- `Not needed`: state why no standalone contract item is required.
- `Ready`: use for telemetry when the event name and feature binding are already known and implementation can proceed through the platform tracking setup.
- `Waiting for event`: use for telemetry when the product intent is clear but the event still needs to be created or confirmed later; follow up through `/complete-master-spec`.
- `Waiting for event` never means the workflow should auto-generate or auto-confirm the ECS event on behalf of Product / Data.

---

## 1. Contract Metadata

| Field | Value |
|-------|-------|
| Feature ID | {FEATURE_ID} |
| Feature Name | {Feature Name} |
| Package Version | rX |
| Package Status | Draft / Approved |
| Source PRD | `features/<feature>/prd.md` |
| Last Updated | |
| Owners | |

## 2. Contract Readout

### 2.1 One-Minute Summary

- New backend work required: TBD
- Confirmed reuse bindings: TBD
- Pending business contract decisions: TBD
- Telemetry follow-up: `none / TBD`

### 2.2 Decision Summary

| Item | Type | Status | Current Readout | Decision Driver / Reference |
|------|------|--------|-----------------|-----------------------------|
| API-001 | Business API | New endpoint required | No source binding has been confirmed yet, so this defaults to new backend work until reuse is proven. | Backend review |
| API-002 | Business API | Pending decision | No standalone request shape confirmed yet. | OQ-00X |
| TEL-001 | Telemetry | Ready / Waiting for event / Reuse existing | Only include telemetry rows when the story has a real tracking need. If telemetry is not needed, omit the standalone section and keep the conclusion in summary only. | Data review |

## 3. Business Contract Items

### 3.1 API-001 — {Business contract item}

- Status: `Pending decision | Confirmed reuse | New endpoint required | Not needed`
- Purpose:
- Consumers:
- Current Readout:
- Decision Driver / Reference:

#### If Status = Confirmed reuse

- Existing Binding:
- Consumed Fields:
- Notes:

Do not copy the full upstream request or response schema into this artifact.

#### If Status = Pending decision

- Linked Question:
- Pending Decision:
- Current Readout: `No standalone request shape confirmed yet.`

#### If Status = New endpoint required

Use this as the default when the feature clearly needs backend support but no existing reusable source has been confirmed yet.

##### Draft Request

| Field | Type | Required | Validation | Notes |
|-------|------|----------|------------|-------|
|  |  |  |  |  |

###### Request Example

```json
{
  "field_name": "value"
}
```

##### Draft Success Response

| Field | Type | Required | Description |
|-------|------|----------|-------------|
|  |  |  |  |

###### Success Example

```json
{
  "data": {}
}
```

##### Error / Failure Handling

| Error Code | HTTP / Status | Meaning | Client Handling |
|------------|---------------|---------|-----------------|
|  |  |  |  |

### 3.2 API-002 — {Business contract item}

Repeat the same status-driven pattern for each business contract item.

## 4. Telemetry / Analytics Contract

Omit this entire section when the story has no real telemetry item beyond `Not needed`.

### 4.1 TEL-001 — {Telemetry need}

- Status: `Ready | Waiting for event | Reuse existing | Pending decision | Not needed`
- Purpose:
- Producers / Consumers:
- Current Readout:
- Decision Driver / Reference:

Use this section as a lightweight telemetry register. Prefer:

- `feature`
- `event_name` when it already exists
- `trigger_point`
- `notes / follow-up`

Do not duplicate ECS integration instructions, generated helper details, or client payload tables here unless those details are themselves unresolved.

#### Telemetry Register

| Field | Value |
|-------|-------|
| Feature | |
| Event Name | |
| Trigger Point | |
| Status | Ready / Waiting for event / Reuse existing / Pending decision / Not needed |
| Platform Notes | |
| Follow-Up | |

If the event contract is still pending or not ready:

- Linked Question:
- Current Readout: `No final event name or payload contract confirmed yet.`

If the event is `Waiting for event`:

- Keep the business intent explicit.
- Record the expected `feature` and proposed `event_name` only if Product / Data has already suggested one.
- Use `/complete-master-spec` to fill the final event binding once Product / Data confirms it.
- Do not auto-generate the event, auto-confirm the name, or invent ECS payload details during package generation or completion.
- Keep the item rendered as a telemetry need or follow-up, not as proof that a real event already exists.

If the event is `Reuse existing`:

- Point to the existing event family or event name.
- State which trigger point should reuse it.

## 5. Shared Contract Rules

| Ref | Rule | Enforcement Point | Notes |
|-----|------|-------------------|-------|
| API-001 |  |  |  |

## 6. Auth / Integration Constraints

| Area | Decision | Notes |
|------|----------|-------|
| Authentication |  |  |
| Authorization |  |  |
| Session / Token |  |  |
| Integration Dependency |  |  |
| Retry / Fallback |  |  |

## 7. Contract Assumptions / Review Notes

### 7.1 Assumptions

- [ ] C-001:

### 7.2 Review Notes

- 

### 7.3 Items That Must Be Tracked In `open-questions.md`

| Item | Why It Needs A Decision | Linked Question |
|------|--------------------------|-----------------|
|  |  | OQ-00X |
