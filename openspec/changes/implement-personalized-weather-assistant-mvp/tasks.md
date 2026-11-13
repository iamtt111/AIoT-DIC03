# Tasks

## 1. Foundation and deployment decisions

- [ ] 1.1 Select the frontend, backend, persistence, authentication, retention, and deletion approach; record the choices and rationale in project documentation, and verify every design open question has an owner or a resolved decision.
- [ ] 1.2 Define the CWA and MOENV endpoint contracts, credential storage, retry/rate-limit policy, and freshness limits; verify the documented calls succeed with approved test credentials or have a documented offline fixture fallback.
- [ ] 1.3 Create versioned persistence models for itineraries, normalized segments, environmental snapshots, decisions, explicit preference versions, feedback, and update candidates; verify schema migration and rollback succeed in a clean test database.
- [ ] 1.4 Establish automated test fixtures for valid, missing, stale, spatially coarse, unavailable, and forecast-versus-observation environmental data; verify the test suite loads each fixture deterministically.

## 2. Itinerary management

- [ ] 2.1 Implement multi-stop itinerary creation and editing with required location, time range, activity, and outdoor-exposure fields; verify valid two-stop itinerary tests pass.
- [ ] 2.2 Implement Taiwan location resolution to coordinates, township, and air-quality area with ambiguity and invalid-input handling; verify ambiguous and unsupported-location tests request correction without fetching data.
- [ ] 2.3 Persist normalized segments with independent identifiers and time ranges; verify a time-range validation test rejects an end time that is not later than its start time.
- [ ] 2.4 Document supported itinerary fields and validation behavior in the product-facing input guidance; verify the documented examples produce valid drafts.

## 3. Environmental data integration and quality

- [ ] 3.1 Implement isolated CWA and MOENV source adapters that retain raw-source metadata alongside normalized values; verify adapter contract tests distinguish source, publication time, fetch time, validity window, and spatial reference.
- [ ] 3.2 Implement selection of CWA forecast data, current-segment MOENV station observations, and eligible MOENV area/date forecasts; verify tests cover each selection path and do not substitute observations for future forecasts.
- [ ] 3.3 Implement temporal/spatial alignment plus `missing`, `stale`, `spatially-coarse`, and `unavailable` quality flags; verify fixture-based tests cover every flag and preserve unaffected data dimensions.
- [ ] 3.4 Document source attribution, observation-versus-forecast labels, and partial-data behavior; verify the result API or view exposes the documented metadata.

## 4. Deterministic recommendation engine

- [ ] 4.1 Implement versioned rules for rain preparation, UV protection, air-quality protection, thermal comfort, and data-quality handling; verify boundary-condition unit tests record a `ruleId` and their input evidence.
- [ ] 4.2 Apply explicit preference versions only to permitted comfort and presentation decisions while protecting mandatory health alerts; verify tests prove preferences cannot remove a triggered health-protection alert.
- [ ] 4.3 Implement immutable `SegmentDecision` records with alerts, evidence, limitations, rule version, and preference version; verify a saved decision can be replayed from its stored inputs with the same result.
- [ ] 4.4 Implement trip-level aggregation that prioritizes health protection, merges duplicate wording, and retains every contributing stop and time window; verify a multi-stop integration test preserves separate rain and air-quality evidence.
- [ ] 4.5 Document the adopted rule sources, thresholds, versioning process, and known limitations; verify each implemented rule links to its documented guidance.

## 5. Evidence presentation and constrained explanations

- [ ] 5.1 Build recommendation result views or endpoints that expose per-stop and trip-level actions, locations, times, sources, data types, update times, rule IDs, and limitations; verify an acceptance test displays all fields for a quality-limited recommendation.
- [ ] 5.2 Implement LLM parsing only as a confirmable itinerary draft and implement explanation prompts from completed structured decisions; verify ambiguous natural-language input requires user confirmation before data retrieval.
- [ ] 5.3 Add explanation-output validation that rejects unrecorded values, sources, changed priorities, and omitted material limitations; verify adversarial-output tests fall back to structured recommendations.
- [ ] 5.4 Document the LLM decision boundary and fallback experience; verify user-facing help states that explanations do not create or alter environmental decisions.

## 6. Preference and feedback workflow

- [ ] 6.1 Implement view, create, update, and delete operations for explicit preference values and versions; verify end-to-end tests show later recommendations reference the selected version.
- [ ] 6.2 Implement post-trip feedback capture for weather match, air-quality match, usefulness, and optional text; verify feedback is associated with its itinerary and segment where provided.
- [ ] 6.3 Implement evidence-backed preference-update candidates with pending, accepted, and rejected states; verify acceptance creates a new preference version and rejection leaves the active version unchanged.
- [ ] 6.4 Document user controls for preferences, feedback, confirmation, deletion, and retention; verify every documented control is available in the implemented interface or API.

## 7. End-to-end verification

- [ ] 7.1 Run end-to-end scenarios for multi-township itineraries, rain, high UV, identical AQI with differing pollutants, current-observation versus next-day forecast selection, and every quality-flag state; verify results are traceable and meet the corresponding OpenSpec scenarios.
- [ ] 7.2 Measure and record API success rate, stale-data handling rate, decision replay success, response time, important-alert omission rate, and limitation disclosure rate; verify the evaluation report separates reliability findings from subjective usefulness feedback.
- [ ] 7.3 Perform security and privacy review of credentials, user itinerary data, preferences, feedback, retention, and deletion; verify identified high-severity findings are resolved or explicitly accepted before release.
