# SPEC: Gemini 3 Native TTS Migration Hardening

- Date: 2026-08-22
- Author: ox-alpha (autonomous run for praxstack)
- Status: reviewed
- Category: maintenance / third-party API integration hardening (bug-fix + feature completion)
- Upstream context: SillyTavern commit 8172dcd ("Update TTS model from Gemini 2.5 to 3.1 in TTS Extension", #5778) added `gemini-3.1-flash-tts-preview` as the default model of the Google Gemini TTS provider but left two gaps.

## Problem

1. **Stale-model desync (frontend).** `public/scripts/extensions/tts/google-native.js` `loadSettings()` calls `$('#google-tts-model').val(this.settings.model)` without checking the saved value against the options that exist in the dropdown. If persisted settings carry a value that is not one of the rendered options (cleared settings, hand-edited settings.json, a model removed in a future release), jQuery sets `selectedIndex` to -1. The next `onSettingsChange()` reads `null` out of the select and persists `model: null`. `generateTts` then posts `model: null`, and the server builds the URL `.../models/null:generateContent`. The user sees an opaque Google 404 instead of a working default.

2. **Unvalidated request boundary (backend).** `src/endpoints/google.js` `/generate-native-tts` destructures `{ text, voice, model }` and passes them straight into `getGoogleApiConfig()`. Missing or empty fields produce malformed upstream URLs (`models/:generateContent`, `models/null:generateContent`) and confusing upstream errors. Per boundary discipline, the endpoint must reject malformed requests with a clear 400 before any outbound call.

3. **No regression coverage.** Neither path has a test. `tests/` has jest wired up; the TTS provider and endpoint are untested.

## Scope

In scope:
- Frontend: reconcile persisted model against the actual dropdown options on load; fall back to the provider default when the saved value is unknown; persist the reconciled value.
- Backend: validate `text` (non-empty string), `voice` (non-empty string), `model` (non-empty string) on `/generate-native-tts`; return 400 `{ error }` on violation, before any network call.
- Tests: unit tests for the new pure helpers on both sides.
- QA evidence: boot the app, drive the TTS settings panel headless, capture screenshots and server/browser logs.

Out of scope:
- Changing the model catalog (2.5 entries stay; upstream owns that list).
- Vertex AI support (option is disabled upstream).
- Any new model IDs not already present in this repo.

## Acceptance criteria (EARS)

- AC1: WHEN saved TTS settings contain a model value that is not among the dropdown options, THE SYSTEM SHALL set the provider settings and the dropdown to the provider default (`defaultSettings.model`) during `loadSettings`, and the reconciled value SHALL be persisted on the next settings save.
- AC2: WHEN saved settings contain no model value or an empty string, THE SYSTEM SHALL behave as AC1 (fall back to default), not leave the dropdown unselected.
- AC3: WHEN saved settings contain a valid model value, THE SYSTEM SHALL keep it unchanged.
- AC4: WHEN `/api/google/generate-native-tts` receives a request whose `text`, `voice`, or `model` is missing, empty, or not a string, THE SYSTEM SHALL respond 400 with a JSON body containing a human-readable `error` field and SHALL NOT make any outbound HTTP call.
- AC5: WHEN all three fields are valid non-empty strings, THE SYSTEM SHALL forward the request unchanged compared to current behavior.
- AC6: THE new frontend reconciliation logic and the backend validation SHALL each be covered by unit tests that pass under `npx jest` with zero regressions in the existing suite.
- AC7: `npm run lint` SHALL pass with the changes applied.
- AC8: A headless browser session SHALL load the app, open the TTS extension settings, and show the Google Gemini TTS provider with the model dropdown defaulting to `gemini-3.1-flash-tts-preview`; evidence (screenshot + server log + console log) SHALL be stored under `.agent-stack/qa/`.
- AC9: The change SHALL NOT alter the wire format of successful TTS requests (same JSON body fields as before).

## Constraints

- Plain JavaScript only (repo has no TS, no build step for frontend).
- 4-space indent, LF, per `.editorconfig`.
- No new npm dependencies.
- Keep the diff minimal; no drive-by refactors.

## Deliverables

- Branch `feat/gemini3-native-tts-hardening` (product change + tests), PR on praxstack/SillyTavern, base `release`.
- Branch `chore/agent-stack-system` (this spec, tickets, reviews, QA evidence, CLAUDE.md routing), pushed to the fork.
- GitHub issues T1-T3 on praxstack/SillyTavern, linked from the PR.
