# Proposal

## Why

Planning an outing currently requires people to combine weather, ultraviolet, and air-quality information from separate official sources, then translate those numbers into personal preparation decisions. The project needs a first usable product that produces source-backed, preference-aware advice without treating an LLM as a weather or health decision-maker.

## What Changes

- Add multi-stop itinerary capture and validation for Taiwanese locations, time ranges, activities, and outdoor exposure.
- Add retrieval, temporal/spatial alignment, and quality disclosure for CWA weather data and MOENV air-quality data.
- Add a deterministic rules engine that produces per-stop and trip-level rain, UV, air-quality, thermal-comfort, and data-quality advice from official data and explicit user preferences.
- Add evidence-preserving result presentation and LLM explanations constrained to the structured decision output.
- Add explicit preference management and post-trip feedback that can propose, but never automatically apply, preference updates.

## Capabilities

### New Capabilities

- `itinerary-management`: Capture, validate, and normalize multi-stop Taiwanese itineraries used for recommendations.
- `environmental-data-integration`: Retrieve and quality-check CWA and MOENV weather and air-quality information for each itinerary stop.
- `personalized-recommendations`: Generate traceable, rules-based per-stop and trip-level preparation advice from aligned data and user preferences.
- `recommendation-explanations`: Present recommendation evidence and limitations, and constrain LLM explanations to the recorded decision output.
- `preference-feedback`: Manage explicit preferences and confirmation-gated preference-update candidates from post-trip feedback.

### Modified Capabilities

- None.

## Impact

- Adds the product's user-facing itinerary, preference, recommendation, evidence, and feedback workflows.
- Requires integrations with CWA weather data and MOENV air-quality observations and forecasts, including their source metadata and availability constraints.
- Requires storage for itinerary records, environmental snapshots, decision evidence, explicit preference versions, feedback, and pending update candidates.
- Requires a rules-versioning approach and a constrained LLM interface for parsing optional natural-language input and explaining completed decisions.
