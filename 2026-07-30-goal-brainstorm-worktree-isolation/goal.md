# Goal Brainstorm Task Worktree Isolation

## Outcome

Implement the approved reusable `agent-workflows:goal-brainstorm` worktree library and thin skill-local script, including exact task identity, recursive submodules, manifest-driven copy and link resources, repair-first validation, and binding all implementation work to prepared task roots until an explicitly authorized merge. Adopt the empty versioned manifest and required instruction and ignore bindings in every repository in the paired specification's exact `Workspace Adoption Set`.

## Source Contracts

- `.spec/2026-07-30-goal-brainstorm-worktree-isolation-spec.md`: dedicated task-specific implementation and acceptance contract.
- `DESIGN.md`: `Goal Brainstorm Worktree` owns the stable provider architecture and library and script boundaries.
- `AGENTS.md`: `Key Directory Map`, `Required Workflows`, and `Commands` own project paths, applicable workflows, and exact repository verification commands.
- `plugins/agent-workflows/skills/goal-brainstorm/SKILL.md`: owns the reusable brainstorm and activation procedure.
- `plugins/agent-workflows/skills/goal-brainstorm/references/specification-contract.md`: owns task-artifact, goal, lifecycle, semantic review, and terminal completion semantics.
- `plugins/agent-workflows/skills/goal-brainstorm/references/worktree-contract.md`: owns worktree identity, bootstrap manifest, resources, submodules, isolation, diagnosis, repair, library and script behavior, and publication handoff.
- `worktree-bootstrap.toml`: owns this repository's concrete bootstrap resource classification.

## Constraints

Implement and validate the provider in `/home/andrey/Projects/agent-plugins/.worktree/2026-07-30-goal-brainstorm-worktree-isolation` on branch `2026-07-30-goal-brainstorm-worktree-isolation` under the task-local first-provider bootstrap in the paired specification. After the library and script pass owner-local tests, use the script first to adopt this worktree, then to prepare the same-prefix task worktrees for every other repository in `Workspace Adoption Set`; seal and validate the complete set before changing any consumer repository. Bind every write, verification command, and Git inspection to its recorded task root. Use main worktrees only as the physical `.spec/` owner where applicable and for isolation validation. Preserve all unrelated dirty state. Keep every initial manifest resource list empty; do not infer project resource semantics. Do not commit, push, merge, or remove task worktrees without a separate explicit request.

## Verification

Satisfy `Verification Obligations`, including the complete `Workspace Adoption Set`, and `Verification Contract` under every applicable standard declared by each affected `AGENTS.md`. Run each repository's exact required checks, the separate skill behavior evaluation, fresh-agent forward tests, full semantic reread, and `Terminal Completion Audit` to a fresh zero-finding pass after the final fix.
