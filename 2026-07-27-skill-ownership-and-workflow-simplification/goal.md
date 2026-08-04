# Skill Ownership And Workflow Simplification

## Outcome

Make `agent-workflows:goal-brainstorm` the sole normative owner of `.spec` task artifacts and simplify `project-standard-audit`, `git-commit`, `goal-review`, and project-local `ozon-dimension-reference` without weakening their behavior or changing any skill identity.

## Source Contracts

- `.spec/2026-07-27-skill-ownership-and-workflow-simplification-spec.md`: complete approved scope, source-to-target ledger, compatibility, publication, and verification contract.
- `plugins/agent-workflows/skills/goal-brainstorm/SKILL.md` and `references/specification-contract.md`: sole `.spec` workflow and lifecycle owner.
- Repository `project-standards`, `plugins/project-standards/skills/project-instruction-developer/**`: instruction and skill ownership contract.
- Repository `project-standards`, `plugins/project-standards/skills/project-standard-audit/**`: generic mechanical inventory and semantic audit contract.
- `plugins/agent-workflows/skills/git-commit/SKILL.md`: commit and publication contract.
- `plugins/agent-workflows/skills/goal-review/SKILL.md`: non-interactive Codex review contract.
- Repository `marketplace-tr-priority`, `.agents/skills/ozon-dimension-reference/**`: project-local Ozon dimension-reference contract.

## Constraints

- Preserve every existing `.spec` file and unrelated user change.
- Retain all plugin and skill identities and every approved behavioral guarantee.
- Leave no copied `.spec` owner, compatibility alias, or mechanically inferred semantic verdict.
- Do not add a commit, tag, or branch suffix to a VCS dependency unless the user explicitly authorizes that revision pin.
- Publish provider changes before dependent consumer revisions and leave every task-owned branch equal to its upstream.

## Verification

Complete every verification and acceptance obligation in the paired specification and every affected repository's current `AGENTS.md`.

After implementation appears complete, repeatedly audit the complete current scope from scratch against this goal, its paired specification, every referenced stable owner, all affected consumer instructions and dependencies, installed provider state, and current repository state. Fix every incomplete, contradictory, stale, missing, or unverified finding, rerun affected verification, and start a new full audit. Finish only after one fresh post-fix audit finds none, all required checks remain successful, user changes are preserved, logical commits are pushed, and every task-owned branch equals its upstream.
