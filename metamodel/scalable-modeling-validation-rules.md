# Scalable Modeling v0.1 — Semantic Validation Rules

JSON Schema validates document **shape**. These rules validate the **meaning of the graph** and therefore belong in a semantic validator.

## Severity

- **ERROR** — contradictory or structurally invalid semantic model.
- **WARNING** — likely modeling problem that may still be intentional.
- **INSIGHT** — Scalable Modeling guidance or risk worth reviewing.

## Foundational decisions

### Models use orthogonal dimensions

A Model does not have one mutually exclusive `role`.

It has:

- `roles`: any combination of `command`, `query`, `derived`
- `authority`: `authoritative`, `non_authoritative`, or `unspecified`
- `stateSource`: `stored`, `event_projection`, `aggregation`, or `unspecified`

This supports CEQS evolution where one event projection can initially serve both commands and queries and be split later.

Example:

```json
{
  "id": "model-user",
  "type": "model",
  "name": "User",
  "roles": ["command", "query"],
  "authority": "authoritative",
  "stateSource": "event_projection"
}
```

### `causes` is discovery semantics

`causes` is a domain-level causal assertion, not executable routing.

It may remain in a model even after explicit Policies are added, but software should never infer a concrete handler, transport, consistency guarantee, or synchronous/asynchronous behavior from `causes` alone.

Explicit behavior is expressed with:

`handles`, `dispatches`, `emits`, `invokes`, `reads`, `updates`, and `projects_into`.

## Graph integrity

### SM000 — Globally unique IDs — ERROR

Element, relationship, view and view-instance IDs must be unique in the document.

### SM001 — Valid references — ERROR

Every `sourceId`, `targetId`, `elementId`, `relationshipId`, and annotation target must resolve.

### SM002 — Relationship endpoint compatibility — ERROR

Allowed endpoints:

| Relationship | Source | Target |
|---|---|---|
| `precedes` | Event | Event |
| `causes` | Command/Event/Query | Command/Event/Query |
| `handles` | Policy | appropriate Message |
| `dispatches` | Policy | Command |
| `emits` | Command Handler or Event Handler | Event |
| `invokes` | Gatekeeper, Event Handler, Data Aggregator | Query |
| `reads` | Policy | Model |
| `updates` | Command Handler, Event Handler, Data Aggregator | Model |
| `projects_into` | Event | Model |
| `belongs_to` | semantic element except Bounded Context | Bounded Context |
| `within_boundary` | Model or Command Handler | Consistency Boundary |
| `defines` | Glossary Term | semantic element |

`causes` is deliberately permissive because it is discovery-level semantics.

## Policy rules

### SM010 — Gatekeeper trigger — ERROR

A Gatekeeper may `handles` Commands only.

### SM011 — Gatekeeper authoritative mutation — ERROR

A Gatekeeper must not `updates` an authoritative Model.

### SM012 — Command Handler trigger — ERROR

A Command Handler may `handles` Commands only.

### SM013 — Command Handler cross-boundary mutation — ERROR

One Command Handler must not update authoritative Models belonging to more than one Consistency Boundary.

If boundaries are still unspecified, no error is produced.

### SM014 — Event Handler trigger — ERROR

An Event Handler may `handles` Events only.

### SM015 — Event Handler idempotency — INSIGHT

Every Event Handler should be treated as potentially receiving the same Event more than once.

Message:

> Ensure this reaction is idempotent or explicitly deduplicated.

### SM016 — Query Handler trigger — ERROR

A Query Handler may `handles` Queries only.

### SM017 — Query Handler side effects — ERROR

A Query Handler must not `dispatches`, `emits`, or `updates`.

### SM018 — Query Handler view count — WARNING

A Query Handler should `reads` at most one logical Model.

If multiple independent Models are read, consider Data Aggregator.

### SM019 — Data Aggregator model count — INSIGHT

A Data Aggregator reading fewer than two Models may be representable as a Query Handler.

## Event and context rules

### SM020 — Public Event ownership — WARNING

A Public Event should belong to one Bounded Context.

### SM021 — Private Event crossing context — WARNING

If an Event is `private`, a Policy in another Bounded Context should not handle it directly.

Suggested resolution:

- expose a Public Event; or
- move the consuming Policy into the owning context.

### SM022 — Multiple bounded-context ownership — ERROR

An Element may have at most one outgoing `belongs_to` relationship.

### SM023 — Event language — WARNING

Event names should normally describe completed facts, usually in past tense.

Natural language detection must be advisory only.

### SM024 — Command language — WARNING

Command names should normally express intent/action.

Natural language detection must be advisory only.

## Model rules

### SM030 — Multiple consistency boundaries — ERROR

A Model may belong to at most one Consistency Boundary in v0.1.

### SM031 — Authoritative command model consistency — WARNING

A Model with `roles` containing `command` and `authority = authoritative` should normally belong to a Consistency Boundary once the architecture has reached spatial/consistency modeling.

No warning should be shown in early temporal discovery mode.

### SM032 — Aggregation source — INSIGHT

A Model with `stateSource = aggregation` will normally have `roles` containing `derived`.

This is guidance, not a hard rule.

### SM033 — Event projection traceability — INSIGHT

A Model with `stateSource = event_projection` should normally have one or more incoming `projects_into` relationships when the model is sufficiently detailed.

## Distribution and consistency analysis

### SM040 — Cross-boundary read — INSIGHT

If a Policy reads a Model outside the Policy's effective consistency boundary, surface:

> This decision may use stale data.

This is especially relevant to Gatekeepers and Event Handlers.

### SM041 — Feedback loop — WARNING

Detect directed cycles through behavior relationships:

`handles → dispatches/emits/invokes → ...`

when the cycle crosses Bounded Contexts or Consistency Boundaries.

Message:

> Feedback loop detected. Examine ordering, stale state, deduplication and time-travel behavior.

Cycles inside a single model are not automatically violations.

### SM042 — Public-event contract use — INSIGHT

When an Event is handled from another Bounded Context but visibility is `unspecified`, suggest deciding whether it should be public.

## Discovery behavior

### SM050 — Incomplete model is valid

Missing context ownership, handlers, boundaries and projections are not errors by themselves.

Scalable Modeling must support temporal discovery before spatial design.

### SM051 — `precedes` does not imply causality

Never infer `causes` from `precedes`.

### SM052 — `causes` does not imply implementation

Never infer a Policy type, delivery mechanism, consistency level, or synchronous/asynchronous execution solely from `causes`.

## Annotation rules

### SM060 — Resolved Hotspot — WARNING

If `status = resolved`, `resolution` should be present.

### SM061 — Unresolved target — ERROR

Every Hotspot or Description target reference must resolve.

## View rules

### SM070 — View references — ERROR

Every semantic element instance must reference an existing Element.

Every relationship instance must reference an existing Relationship.

### SM071 — Duplicate semantic appearances — VALID

The same semantic element may appear multiple times in one View or across many Views.

Visual identity and semantic identity are intentionally separate.

## Suggested validation phases

A tool should validate in this order:

1. JSON Schema structure.
2. ID/reference integrity.
3. Relationship endpoint compatibility.
4. Hard semantic invariants.
5. Modeling warnings.
6. Scalable Modeling insights.

This separation keeps exploratory workshops fluid while still allowing strict validation when the model matures.
