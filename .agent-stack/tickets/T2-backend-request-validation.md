# T2: Validate `/generate-native-tts` request boundary

- Priority: P1 (bad upstream calls, opaque errors)
- Area: `src/endpoints/google.js`
- Spec: `.agent-stack/specs/2026-08-22-gemini3-native-tts-hardening.md` (AC4, AC5, AC6)

## Problem

The endpoint destructures `{ text, voice, model }` and forwards them without validation. Missing/empty/non-string fields produce malformed upstream URLs (`models/null:generateContent`) and confusing Google-side errors.

## Task

1. Add an exported pure validator, e.g. `validateNativeTtsRequest(body)`, returning `{ ok: true }` or `{ ok: false, error }` where `error` names the first missing/invalid field.
2. Call it at the top of the `/generate-native-tts` handler; on failure respond `400` with `{ error }` and return before `getGoogleApiConfig()` or any `fetch()`.
3. Unit tests: each of the three fields missing / empty / non-string yields ok:false with a named error; a valid body yields ok:true; no outbound call happens on failure (assert via handler short-circuit logic or validator purity).

## Acceptance

- AC4, AC5, AC6 hold.
- Success path wire format unchanged (AC9).
