# Goal Brainstorm Task Worktree Isolation

## Required Outcome

`agent-workflows:goal-brainstorm` must prepare one isolated task worktree before it authors tracked task contracts or starts specification implementation. The physical `.spec/` directory must remain in the coordinating repository main worktree. Each affected linked worktree must expose that directory through one relative `.spec` symbolic link.

The specification file, goal file, task branch, and linked-worktree directory must use one exact common prefix. For this task, that prefix is `2026-07-30-goal-brainstorm-worktree-isolation`.

Tracked task changes must not appear in a main worktree before an explicitly authorized merge. The workflow must diagnose invalid state and repair it when the correction is deterministic and preserves user changes. It must request user input only when ownership, desired content, or the safe correction is ambiguous.

## Verified Current State

- The coordinating repository is `agent-plugins`.
- Its main worktree is on `main` at `97aeb344568b0ea48a199c04e9ede4518ff308ed`.
- Its registered task worktree is `/home/andrey/Projects/agent-plugins/.worktree/2026-07-30-goal-brainstorm-worktree-isolation` on the same-prefix branch at that baseline.
- The task worktree's `.spec` link is the relative target `../../.spec` and resolves to the coordinating main worktree's physical task-artifact directory.
- Its initial `worktree-bootstrap.toml` is valid schema version 1 with all four resource lists empty.
- Neither target library nor target script exists at the selected baseline, so this task must bootstrap their first implementation through its predecessor workflow contract.
- No persistent goal is active.
- Three existing plugin-version changes are unrelated user changes and must remain unchanged.
- The current `goal-brainstorm` workflow does not create or bind a task worktree.
- The current `/.spec/` ignore rule ignores a directory but does not ignore a `.spec` symbolic link.
- Git accepts an arbitrary linked-worktree path. Its `.git/worktrees` path is internal metadata and does not define a project-local directory convention.
- The project naming contract therefore selects the singular project-local root `.worktree/`.
- Git 2.43.0 successfully created an isolated test worktree with recursive nested submodules at their recorded gitlinks.
- Git documents incomplete submodule support for multiple superproject worktrees. The workflow must verify actual submodule state and must not infer success from command completion alone.
- Historical project-local worktree tools used concrete environment and profile paths. Those deleted tools are evidence only and are not target owners.
- The current `lib/*/tool` trees originated in the initial provider migration commit `af74941`; plugin scaffolding does not generate that layout.
- Existing `agent-workflows` support owners use `tool/` for executable CLI entrypoints. The two audit owners use `tool/lib/` for implementation shared by multiple entrypoints. Neither pattern justifies placing a reusable goal-brainstorm library below `tool/`.
- `code-antipattern-audit/tool/lib/report_contract.py` serves two production CLI entrypoints and `section-audit/tool/lib/audit_contract.py` serves three. `subagent-transport/tool/lib/subagent_track.py` has only one production CLI caller, so that existing nested library layer has no demonstrated second production owner and remains an out-of-scope structural finding.
- Current installed plugins demonstrate a separate skill-local `scripts/` entrypoint convention and a direct plugin `lib/` implementation convention.
- Exactly 20 immediate child directories of `/home/andrey/Projects` are standalone Git repository roots. None currently has a tracked root `worktree-bootstrap.toml`.
- Four current main checkouts use `.worktrees/`, none ignores the singular `.worktree/`, and none of their current `.spec/` patterns ignores a real `.spec` symbolic link.
- Five current main checkouts contain unrelated dirty state. The workspace adoption must preserve that state and must not require clean main worktrees.

## Scope

This task changes the stable provider contract, one reusable worktree library, one thin skill-local script, owner-local tests, and skill behavior evaluation for `agent-workflows:goal-brainstorm`.

The reusable library path is `plugins/agent-workflows/lib/goal-brainstorm/worktree.py`. The executable path is `plugins/agent-workflows/skills/goal-brainstorm/scripts/worktree.py`. Owner-local tests live under `plugins/agent-workflows/lib/goal-brainstorm/test/`. No `plugins/agent-workflows/lib/goal-brainstorm/tool/` owner exists.

The worktree bootstrap manifest is mandatory for each participating top-level repository and each submodule whose repository content or local bootstrap resources the task uses. A read-only initialized submodule at its recorded gitlink does not receive a manifest change only because it was initialized. The workflow creates a missing initial manifest inside the task worktree before implementation and applies it before implementation starts.

The current `project-standards:project-instruction-developer` contract remains the owner of consumer instruction structure. A consumer `Key Directory Map` must map `.spec/`, `.worktree/`, and `worktree-bootstrap.toml` and name `agent-workflows:goal-brainstorm` as their reusable semantic owner. Consumer instructions must not copy the manifest schema or workflow rules.

This task also performs one explicitly approved multi-project instruction and manifest adoption across `Workspace Adoption Set`. Each adopted project receives the empty versioned manifest, compliant path bindings, and ignore behavior in its task worktree. Concrete copy or link resources remain empty until that project explicitly classifies a real dependency.

The consumer instruction phase applies `agent-workflows:instruction-migration` because the user explicitly approved one coordinated multi-owner instruction migration.

## Workspace Adoption Set

The workspace adoption set contains each immediate child of `/home/andrey/Projects` whose canonical path equals its own `git rev-parse --show-toplevel` result:

- `agent-plugins`
- `arasta-link-docs`
- `brand-size-chart`
- `browser-runtime`
- `compose-milvus`
- `compose-mysql`
- `marketplace-infrastructure`
- `marketplace-next-co-uk`
- `marketplace-parser`
- `marketplace-tools`
- `marketplace-tr`
- `marketplace-tr-priority`
- `project-standards`
- `scrapy-next-deprecated`
- `v0-arasta-link`
- `vpn-runtime`
- `workflow-container-contract`
- `workflow-container-runtime`
- `workflow-control-center`
- `workflow-infrastructure`

Temporary clones, plugin caches, linked worktrees, and nested submodule checkouts are not independent targets of this one-time workspace adoption. A later change to one submodule follows its own repository-boundary contract.

Every listed repository uses the common task branch and `.worktree/` basename. Its task worktree links `.spec` to the coordinating physical directory in `agent-plugins`. No tracked adoption change is written to a listed main worktree before an explicitly authorized merge.

## Non-Goals

- Do not add worktree orchestration to `project-standards`.
- Do not discover or mutate repositories outside `Workspace Adoption Set`.
- Do not create manifest changes in read-only submodules only because recursive initialization occurred.
- Do not hardcode application names, environment names, profile names, or application paths in the provider.
- Do not infer copy or link behavior from file or directory names.
- Do not refactor unrelated existing plugin support-owner layouts only to make their directory trees look uniform.
- Do not commit, push, merge, or remove a task worktree without the applicable explicit authorization.
- Do not provide an operating-system sandbox or change main-worktree permissions.
- Do not alter unrelated plugin-version changes.

## Stable Ownership

- Root `DESIGN.md` owns the provider architecture, public workflow-tool surface, and cross-project ownership boundary.
- Root `AGENTS.md` owns this repository's concrete path bindings.
- `plugins/agent-workflows/skills/goal-brainstorm/SKILL.md` owns the task procedure and activation sequence.
- `plugins/agent-workflows/skills/goal-brainstorm/references/specification-contract.md` owns task-pair selection, placement, approval, retention, and completion semantics.
- `plugins/agent-workflows/skills/goal-brainstorm/references/worktree-contract.md` owns the reusable worktree, manifest, isolation, repair, and validation contract.
- The paired task artifacts own only this implementation objective and its task-specific acceptance boundary.

This task uses Dedicated Implementation Specification mode because it adds one stateful workflow, one library and script boundary, one manifest schema, recovery behavior, one explicit workspace adoption, and shared acceptance criteria across several owners.

## Task Identity And Paths

The common prefix is the complete specification filename without `-spec.md` and the complete goal filename without `-goal.md`.

For one coordinating repository:

```text
.spec/<common-prefix>-spec.md
.spec/<common-prefix>-goal.md
.worktree/<common-prefix>/
refs/heads/<common-prefix>
```

For a multi-repository task, each affected top-level repository uses the same branch name and linked-worktree basename. Only the coordinating main worktree physically owns the task pair. Each affected top-level task worktree links `.spec` to that one physical directory.

The workflow must reject a common prefix that cannot be used unchanged as a Git branch basename and one filesystem directory basename. It must fail closed on an unrelated existing branch, worktree registration, filesystem path, task pair, or private workflow state with the same identity.

## Worktree Bootstrap Manifest

Each affected repository boundary must contain one tracked root `worktree-bootstrap.toml` with this versioned shape:

```toml
schema_version = 1

[resource]
copy_optional_path_list = []
copy_required_path_list = []
link_optional_path_list = []
link_required_path_list = []
```

All four lists contain exact POSIX paths relative to their owning repository boundary. Globs, absolute paths, empty path values, `.` segments, `..` segments, duplicate paths, nested overlaps, `.git`, `.spec`, and the task-worktree root are forbidden. Unknown fields and unsupported schema versions are errors.

The provider defines behavior by resource class. Concrete path bindings exist only in the owning project or submodule manifest.

- A copy resource is one isolated snapshot. The task may modify its destination without changing the source.
- A link resource is one explicitly shared resource that must remain unchanged during implementation.
- A required resource must exist and satisfy its class contract.
- An absent optional resource is reported and skipped.

Each destination must be ignored as its real destination object type. A copy preserves regular files, directories, permissions, and safe internal symbolic links. It rejects special filesystem objects and symbolic links whose resolved target escapes the copied source tree. A link uses one relative symbolic link to the main-worktree source.

The `.spec` link is reserved workflow behavior and never appears in the manifest. After sealing, the specification and goal are immutable workflow inputs.

## Preparation Lifecycle

The workflow uses these observable states:

1. `designing`: inspect current owners, repository boundaries, goal state, and task dependencies without tracked task changes.
2. `design_approved`: write the approved specification draft into the coordinating main worktree's physical `.spec/`.
3. `worktree_created`: create one task branch and linked worktree for each affected top-level repository from its selected committed base.
4. `repository_prepared`: initialize every recursive submodule at its recorded gitlink, create or validate each participating manifest, author minimum durable ignore rules, install any exact temporary local excludes needed for first adoption, materialize declared resources, and create the `.spec` link.
5. `contracts_authored`: update approved stable owner documents only in task worktrees and update the physical specification through the link.
6. `goal_ready`: pass semantic review, create the paired goal through the link, validate and seal the complete contract set, and show it.
7. `active`: create the persistent goal and run all implementation, verification, and Git inspection against the recorded task-worktree roots.

The workflow must not enter `contracts_authored`, `goal_ready`, or `active` while a required repository boundary is unprepared.

Before linked-worktree creation, the workflow may add one exact temporary local exclude for `.worktree/` in each participating top-level repository whose committed main-worktree ignore rules do not yet cover it. During first adoption, it may also add the minimum exact root-relative excludes needed for the `.spec` link and declared bootstrap destinations. Every local Git metadata change must be recorded. During preparation, each task worktree must add and verify minimum durable tracked ignore behavior. A temporary local exclude must be removed only after the corresponding durable rule is present in that repository's merged main branch.

Creating a missing initial `worktree-bootstrap.toml` and authoring its minimum durable ignore rules are the only tracked preparation writes allowed before every required repository reaches `repository_prepared`. All other tracked instruction, design, code, and test changes wait until the complete repository set is prepared. The task-local first-provider bootstrap in `Compatibility And Migration` is governed by the predecessor workflow contract rather than this not-yet-implemented target lifecycle.

The durable ignore contract must cover the physical `.spec` directory, the `.spec` symbolic link, and the `.worktree/` container by behavior. It must not require one equivalent textual pattern.

## Submodule Contract

The workflow must synchronize recursive submodule URLs and initialize every recursive submodule at the exact gitlink recorded by its parent worktree. It must verify the complete recursive status after initialization.

An uninitialized submodule, a clean submodule at the wrong revision, or stale recursive URL configuration has a deterministic repair: synchronize configuration and update the submodule to the recorded gitlink. A dirty submodule is not automatically reset. The workflow must inspect its branch, status, diff, untracked paths, and relation to the active task before deciding whether it is intended task work or ambiguous external work.

Each initialized submodule owns its own instruction boundary. A parent manifest must not bind paths inside a submodule. A submodule creates or applies `worktree-bootstrap.toml` only when it becomes a participating repository because the task changes its content or uses its local bootstrap resources.

The workflow must not move a linked worktree that contains submodules. Removal remains outside goal activation and must follow explicit publication or cleanup authorization.

## Isolation State And Validation

The workflow stores private run state in the linked worktree's Git administration path resolved by `git rev-parse --git-path`. It must not store harness state in `.spec/`.

The state records:

- the common prefix and coordinating repository;
- each main-worktree and task-worktree canonical path;
- each base commit, task branch, and Git common directory;
- the pre-task main index and working-state fingerprints;
- unrelated dirty-path fingerprints;
- recursive gitlinks and prepared submodule roots;
- manifest content and materialization results;
- the specification and goal hashes after sealing;
- every temporary local exclude added by the workflow.

Every implementation, verification, or Git phase must resolve its exact Git top level and compare it with the recorded task worktree. A call that starts from another path must be rerouted to the recorded task worktree when that correction is deterministic.

Validation must confirm that:

- each task branch and linked worktree still have the recorded identity;
- no task-owned tracked change exists in a main worktree;
- unrelated main-worktree changes retain their recorded content;
- each `.spec` link resolves to the coordinating physical directory;
- sealed task artifacts retain their hashes;
- each recursive submodule is initialized and has valid intended state;
- each manifest and resource retains its required structural contract.

Independent main-worktree commits or dirty paths that are provably outside task provenance and do not overlap task changes or prepared resources must be preserved and recorded as unrelated state without stopping the task. Overlapping state or unclear provenance remains ambiguous and must not be reverted.

## Diagnosis And Repair

Validation failure starts diagnosis and repair. It does not immediately end the workflow.

The workflow must identify the changed object, its current owner, its recorded state, the most likely producing operation, and the smallest safe correction. It must then apply every deterministic repair that preserves user-authored changes.

Deterministic repairs include:

- rerouting wrong-root execution to the recorded task worktree;
- using `git worktree repair` for a valid worktree with damaged administrative linkage;
- recreating a missing or incorrect `.spec` link when its path has no independent content;
- adopting one fully matching inactive task worktree and reconstructing absent private state after an interrupted or tool-less bootstrap;
- synchronizing and initializing missing submodules;
- updating one clean drifted submodule to its recorded gitlink;
- recreating an incomplete provider-created copy when its destination has no independent changes;
- creating a missing initial manifest in the task worktree;
- recording a provably independent, non-overlapping main change as unrelated user state;
- restoring a provider-created local exclude or other private state;
- moving an agent-created main-worktree patch into the task worktree and restoring the exact recorded main preimage when provenance and both contents are unambiguous.

After each repair set, the workflow must repeat complete validation from current state. It continues the diagnose, repair, and validate cycle until validation is clean or one genuine ambiguity or external blocker remains.

The workflow asks the user only when repair would overwrite or reinterpret possible user work, choose between multiple valid owner contracts, select unknown resource semantics, use unavailable external state, or perform an unauthorized destructive or publication action.

## Goal Activation And Later Merge

After the target provider exists, goal activation is allowed only from sealed `goal_ready` after a fresh complete worktree validation. The persistent objective must name the goal file and bind implementation to the exact prepared worktree paths. Activation of this first implementation remains governed by the task-local predecessor-workflow bootstrap in `Compatibility And Migration`; it does not weaken the target activation rule for later goals.

The brainstorm workflow does not commit, push, or merge. A later explicit Git request uses `agent-workflows:git-commit`. Until that separate action merges the task branch, validation requires the main tracked baseline to remain unchanged by the task.

The retained task pair remains in the coordinating main worktree after merge, completion, abandonment, or worktree cleanup.

## Compatibility And Migration

The former project-local `.worktrees/` convention is not retained as a compatibility path. A project changes to `.worktree/` when it adopts this contract.

A missing manifest is a supported adoption state only before implementation. The workflow creates and validates the initial manifest in the task worktree. After merge, absence of the tracked manifest is a project-contract violation.

Existing ignored local resources receive no inferred strategy. Their owning project must classify each required resource as copy or link before implementation can depend on it.

This task is the first implementation of the target worktree interfaces and is currently governed by the predecessor `goal-brainstorm` workflow, which cannot invoke code that does not exist at its selected baseline. Before goal activation, the already registered `agent-plugins` task worktree must pass the complete manual conformance gate and remains the only root in which provider implementation may begin.

After the library and script pass their owner-local tests, the script must first adopt the current `agent-plugins` worktree and reconstruct its private state. It must then create and prepare the same-prefix task worktrees for the other 19 repositories in `Workspace Adoption Set`, validate the complete 20-project set, and seal the unchanged task pair. Before that full seal passes, consumer task worktrees may contain only provider-created manifest and minimum ignore preparation changes.

## Verification Obligations

Owner-local automated behavior tests must cover:

- the direct reusable library path, the thin skill-local script path, and absence of the rejected `lib/goal-brainstorm/tool/` path;
- owner-local library and script tests under `plugins/agent-workflows/lib/goal-brainstorm/test/`;
- script delegation to the library without duplicated repository behavior;
- exact common-prefix derivation for specification, goal, branch, and worktree;
- preparation with arbitrary resource names and no application-specific path knowledge;
- physical `.spec` ownership and relative symbolic links;
- object-type-independent ignore validation;
- isolated copy mutation and unchanged sources;
- relative link creation and link-source drift detection;
- required and optional resource behavior;
- manifest schema, path escape, duplicate, overlap, reserved-path, and unknown-field failures;
- recursive nested submodule initialization at exact gitlinks;
- missing, clean-drifted, dirty, and unavailable submodule states;
- preservation of pre-existing and later independent main-worktree state;
- overlapping main drift and leaked task-patch classification;
- wrong-root rerouting;
- deterministic repair of administrative linkage, links, copies, manifests, and clean submodules;
- refusal to overwrite ambiguous user changes;
- collision and partial-bootstrap recovery;
- acceptance and rejection boundaries for initial self-hosting, provider-only work before adoption, and complete sealing before consumer changes;
- active task-artifact immutability;
- complete validation after every repair.

The workspace adoption must additionally verify:

- the exact 20-project `Workspace Adoption Set` from current filesystem and Git evidence;
- one same-prefix task worktree and relative `.spec` link for every listed repository;
- one empty schema-versioned `worktree-bootstrap.toml` in every listed task worktree;
- compliant `.spec`, `.worktree`, and manifest bindings in every listed root `AGENTS.md`;
- durable ignore behavior for physical `.spec`, linked `.spec`, and `.worktree/`;
- removal of active `.worktrees/` compatibility paths from adopted target state;
- preservation and correct classification of every unrelated dirty main-worktree change;
- no tracked adoption change in any listed main worktree before merge;
- each listed repository's applicable instruction, structural, and verification contracts.

Tests must use temporary Git repositories and real Git commands. They must verify observable filesystem and Git behavior instead of private call order.

The provider change must pass:

- Black on all changed Python scope;
- targeted owner-local tests;
- `pytest -q`;
- goal-brainstorm skill validation;
- all three plugin validations required by root instructions;
- deterministic `skill_behavior_eval/corpus-v1.json --list`;
- the separate model behavior-evaluation phase for changed goal-brainstorm output semantics;
- fresh-agent forward tests that receive the revised skill and realistic requests without the intended answer;
- semantic reread of `AGENTS.md`, both provider design and instruction owners, the revised skill contracts, this specification, and the paired goal;
- Terminal Completion Audit to one fresh zero-finding pass after the final fix.

The behavior corpus must verify the worktree-before-tracked-change sequence, exact task identity, mandatory manifest, repair-first handling, no automatic merge, and negative activation outside `goal-brainstorm`.
