# T1: Reconcile persisted Google TTS model with available options

- Priority: P1 (user-facing broken state)
- Area: `public/scripts/extensions/tts/google-native.js`
- Spec: `.agent-stack/specs/2026-08-22-gemini3-native-tts-hardening.md` (AC1-AC3, AC6)

## Problem

`loadSettings()` applies `this.settings.model` to the model `<select>` with no existence check. Unknown saved values (empty string, removed model id, corrupt settings) leave the select unselected; the next change event persists `model: null` and generation posts `models/null:generateContent` upstream.

## Task

1. Extract a pure helper, e.g. `resolveTtsModel(savedModel, availableModels, fallbackModel)`, that returns `savedModel` when it is a non-empty string contained in `availableModels`, otherwise `fallbackModel`. Export it for unit testing.
2. In `loadSettings()`, read the option values from the rendered select, run the helper, and apply the result to both `this.settings.model` and the DOM. If reconciliation changed the value, save settings so the fix sticks.
3. Unit tests: valid value kept; unknown value falls back; empty/undefined/null falls back; non-string falls back.

## Acceptance

- AC1, AC2, AC3, AC6 hold.
- No change to the dropdown's option list or labels.
