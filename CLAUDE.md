# CLAUDE.md

Local agent tooling notes for the praxstack fork of SillyTavern. Product conventions live in CONTRIBUTING.md and .editorconfig.

## Skill routing

When the user's request matches an available skill, invoke it via the Skill tool. When in doubt, invoke the skill.

Key routing rules:
- Product ideas/brainstorming → invoke /office-hours
- Strategy/scope → invoke /plan-ceo-review
- Architecture → invoke /plan-eng-review
- Design system/plan review → invoke /design-consultation or /plan-design-review
- Full review pipeline → invoke /autoplan
- Bugs/errors → invoke /investigate
- QA/testing site behavior → invoke /qa or /qa-only
- Code review/diff check → invoke /review
- Visual polish → invoke /design-review
- Ship/deploy/PR → invoke /ship or /land-and-deploy
- Save progress → invoke /context-save
- Resume context → invoke /context-restore
- Author a backlog-ready spec/issue → invoke /spec

## GBrain Search Guidance (configured by /sync-gbrain)
<!-- gstack-gbrain-search-guidance:start -->

GBrain is available on this machine (`gbrain` CLI). Prefer gbrain over Grep when the
question is semantic or the exact identifier is unknown:
- "Where is X handled?" → `gbrain search "<terms>"` / `gbrain query "<question>"`
- Symbol lookup → `gbrain code-def <symbol>` / `gbrain code-refs <symbol>`
- Callers/dependents → `gbrain code-callers <symbol>`

Grep stays right for exact strings, regex, globs. Run `/sync-gbrain` to refresh the index.

<!-- gstack-gbrain-search-guidance:end -->

## Agent stack

- Spec-driven workflow artifacts live in `.agent-stack/` (specs, tickets, reviews, qa).
- Branches: `chore/agent-stack-system` carries tooling/docs; feature branches carry product changes only.
- PRs target this fork (praxstack/SillyTavern), base branch `release`.
