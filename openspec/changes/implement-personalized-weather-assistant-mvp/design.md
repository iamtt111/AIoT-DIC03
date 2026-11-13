# Design

## Context

See `proposal.md` for motivation. The repository currently contains the research proposal and target-system design, but no implemented product or durable OpenSpec capability. The implementation must combine CWA and MOENV data without turning observations into forecasts, make every recommendation reproducible, and keep LLMs outside of numerical and health-protection decisions.

## Goals / Non-Goals

**Goals:**

- Establish a separable end-to-end pipeline from itinerary input to evidence-backed recommendations.
- Preserve the data, rule, and preference version that produced each recommendation.
- Gracefully deliver unaffected weather advice when one environmental data source is unavailable.
- Make data limitations first-class output instead of hiding them in generated prose.

**Non-Goals:**

- Predict weather or air quality beyond official data coverage.
- Build route planning or infer travel conditions between itinerary stops.
- Diagnose health conditions or replace official health guidance.
- Automatically learn or apply long-term preference changes.

## Decisions

### Use a staged, structured decision pipeline

The system will process each itinerary as `validate -> normalize -> fetch -> align/quality-check -> decide -> aggregate -> explain -> present`. Each stage emits structured records consumed by the next stage. This keeps acquisition, decision-making, and language generation independently testable.

Alternative considered: let an LLM orchestrate data retrieval and decide recommendations. Rejected because it cannot provide deterministic rule application or reliable, replayable evidence.

### Model the recommendation as an immutable decision record

Each `SegmentDecision` will reference the normalized segment, environmental snapshot identifiers, rule identifier/version, explicit preference version, alerts, evidence fields, and limitations. `TripRecommendation` will reference rather than flatten these decisions so merged reminders retain their per-stop proof.

Alternative considered: save only the rendered recommendation text. Rejected because text cannot be replayed, audited, or safely re-explained after source data changes.

### Separate environmental source adapters from alignment and quality policy

Source adapters will retrieve raw CWA and MOENV payloads plus metadata. A separate alignment layer selects CWA forecast data by township/time, MOENV live observations for current segments, and official MOENV air-quality forecasts only when their air-quality-area/date coverage applies. It records missing, stale, spatially coarse, and unavailable states without synthesizing values.

Alternative considered: normalize all sources to a single current-value schema. Rejected because it obscures the material distinction between a station observation and an area/date forecast.

### Treat rules as versioned, deterministic policy

The rules engine will consume validated snapshots, activity/exposure fields, and explicit preferences. It will evaluate rain, UV, air quality, thermal comfort, and data quality separately, then assign priority and actions. Health-protection rules remain mandatory; preferences influence comfort advice and presentation order only.

Alternative considered: store thresholds only in prompt text. Rejected because prompt-only thresholds are not versionable, directly testable, or independently auditable.

### Constrain the LLM through structured input and output validation

Natural-language itinerary additions may be parsed into a draft that the user confirms. Explanations will receive only completed structured decisions and must pass a consistency check against the evidence, timing, limitations, and priority. On timeout or validation failure, the UI renders the structured result directly.

Alternative considered: disable LLM entirely in the MVP. Rejected because concise explanations and clarification are a stated product outcome; the constraint boundary controls its risk instead.

### Keep preference feedback as a proposal workflow

Explicit preferences, feedback records, and update candidates are separate record types. A candidate requires user acceptance before a new preference version becomes active; feedback cannot weaken an important health-protection rule.

Alternative considered: update preferences continuously from feedback. Rejected because it makes personalization opaque and can create unsafe or surprising recommendation changes.

## Risks / Trade-offs

- [Official source schema, availability, or quota changes] -> Isolate source adapters, validate source payloads, record failures, and surface partial results.
- [A representative station or area-level forecast may not reflect an exact location] -> Retain spatial reference and distance, set quality flags, and limit precise conclusions.
- [Thresholds need authoritative review] -> Version rules, link each health-protection rule to its adopted official guidance, and test boundary cases before release.
- [LLM output might omit a limitation or invent a detail] -> Validate it against the immutable decision record and fall back to structured presentation.
- [Stored itineraries and preferences are personal data] -> Define authentication, retention, deletion, and access controls before production release.

## Migration Plan

1. Deploy source adapters and the data model behind an internal feature flag with recorded sample itineraries.
2. Validate data alignment, quality flags, deterministic rule replay, and explanation consistency against the scenarios in the change specs.
3. Enable itinerary creation and structured recommendations first; enable LLM explanations only after consistency validation is active.
4. Roll back by disabling recommendation generation and LLM explanation independently; retain collected source snapshots and decision records for diagnosis according to the retention policy.

## Open Questions

- Which CWA and MOENV endpoints, credentials, rate limits, and freshness limits will be adopted for the first deployment?
- Which official guidance will define each initial rule threshold and review cadence?
- What authentication, retention period, and deletion workflow are appropriate for the deployment context?
