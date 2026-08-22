# SillyTavern — Agent Guide

Third-party SillyTavern upstream clone. Keep local branches; never push to upstream.

## Agent skills

### Issue tracker

No GitHub remote here. Work is tracked in `TASKS.md` at the root. See `docs/agents/issue-tracker.md`.

### Triage labels

Default five-role state vocabulary (`needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`) plus `bug`/`enhancement` category roles and `area:`/`platform:`/`priority:` facets. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context: root `CONTEXT.md` (domain vocabulary and document index) and `docs/adr/`. See `docs/agents/domain.md`.

### Skill discovery

Start each session with the `using-superpowers` skill to discover available skills before answering. The gstack suite (browse, ship, investigate, retro, and related) is installed host-side under `~/.agents/skills/`; invoke it by name when a task matches. Per-role model mapping for pstack lives globally at `~/.config/pstack/models.md`. For any user-facing prose drafted here, run `stop-slop` or `humanize` over it before shipping.
