# T3: QA evidence pass for the TTS change

- Priority: P1 (gate before PR)
- Spec: `.agent-stack/specs/2026-08-22-gemini3-native-tts-hardening.md` (AC7, AC8)
- Tools: jest, eslint, playwright/headless chromium, server log capture

## Task

1. Run `npx jest` (tests/) and `npm run lint`; record outputs.
2. Boot the server (`node server.js`) on a scratch data dir; capture stdout to `.agent-stack/qa/server.log`.
3. Headless-drive the UI: open the app, open TTS extension settings, select Google Gemini TTS provider, verify the model dropdown shows `gemini-3.1-flash-tts-preview` selected; screenshot to `.agent-stack/qa/`.
4. Inject a stale model value into the provider settings and reload; verify reconciliation back to default (AC1) with screenshot.
5. Record browser console output; note anything unexpected.
6. Honest limits: no Google API key in this environment, so live audio generation is NOT exercised; covered instead by unit tests + mocked behavior. State this in the QA report.

## Acceptance

- `.agent-stack/qa/` contains: server.log, console log capture, at least 2 screenshots (default state, reconciled state), and a QA report md with pass/fail per AC.
- All gates green before the PR is raised.
