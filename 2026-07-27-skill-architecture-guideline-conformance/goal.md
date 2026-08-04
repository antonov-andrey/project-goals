# Skill Architecture Guideline Conformance

## Outcome

Implement the approved multi-repository skill architecture correction so all current plugins and 42 skill identities retain their intended boundaries while transport semantics, discovery metadata, progressive disclosure, shared audit orchestration, deterministic metadata validation, behavioral evaluation, and authorized cleanup conform to the paired specification.

## Source Contracts

- `.spec/2026-07-27-skill-architecture-guideline-conformance-spec.md`: complete task-specific integration, source-to-target, compatibility, and acceptance contract.
- `DESIGN.md`: stable `agent-plugins` provider and plugin architecture.
- `plugins/agent-workflows/lib/subagent-transport/protocol.md`: canonical subagent transport owner.
- `plugins/agent-workflows/lib/section-audit/protocol.md`: canonical shared section-audit owner.
- `plugins/agent-workflows/lib/sequential-batch/protocol.md`: canonical sequential-batch owner.
- Repository `project-standards`, `plugins/project-standards/skills/project-instruction-developer/references/skill-model.md`: canonical reusable skill-model and metadata owner.
- Repository `project-standards`, `plugins/project-standards/skills/sqlalchemy-developer/`: canonical SQLAlchemy standard owner.
- Repository `marketplace-tr-priority`, `.agents/skills/**`: canonical retained project-local skill owners.

## Constraints

- Retain all approved plugin and skill identities.
- Preserve ignored user files under `marketplace-tr-priority/.agents/skills/trendyol-size-chart-parse/scratch/images/**`.
- Do not weaken behavioral expectations, semantic invariants, provider ownership, or repository verification to make checks pass.
- Leave no compatibility aliases, duplicate old owners, stale provider identities, or transition-only reference paths.

## Verification

Complete every verification and acceptance obligation in the paired specification and the applicable `AGENTS.md` contracts of `agent-plugins`, `project-standards`, and `marketplace-tr-priority`.

After implementation appears complete, repeatedly audit the complete current scope from scratch against this goal, its paired specification, every referenced stable source owner, all current provider source and installed state, and every affected consumer boundary. Fix every incomplete, contradictory, stale, missing, or unverified finding, rerun affected verification, and start a new full audit. Finish only after one fresh post-fix audit finds none, all required checks remain successful, task-owned worktrees are clean, every logical commit is pushed, and each branch equals its upstream.
