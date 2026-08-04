# Skill Ownership And Workflow Simplification

## Outcome

Make `agent-workflows:goal-brainstorm` the sole normative owner of harness-neutral `.spec` task artifacts, remove every copied `.spec` contract from project documentation and generic standards auditing, and simplify four retained skills without weakening their behavior.

The final state keeps all current plugin and skill identities, preserves every retained `.spec` pair, separates full standards audit from standalone instruction classification, removes repeated workflow prose from `git-commit` and `goal-review`, and gives `ozon-dimension-reference` conditional progressive disclosure while keeping its project-local domain ownership.

## Coordinating And Source Owners

This is a dedicated multi-repository implementation specification coordinated by repository `agent-plugins`.

- `plugins/agent-workflows/skills/goal-brainstorm/references/specification-contract.md` is the sole normative owner of `.spec` structure, contents, lifecycle, ignore boundary, retention, deletion authorization, coordinating-repository selection, semantic review, and terminal completion audit.
- `plugins/agent-workflows/skills/goal-brainstorm/SKILL.md` owns the workflow that creates, changes, inspects, and audits one `.spec` pair and activates its persistent goal.
- Repository `project-standards`, `plugins/project-standards/skills/project-documentation-developer/**` owns only stable project design and maintained documentation classification.
- Repository `project-standards`, `plugins/project-standards/skills/project-standard-audit/**` owns generic workspace inventory and complete standards semantic audit, but not task-artifact semantics.
- `plugins/agent-workflows/skills/git-commit/SKILL.md` owns commit and publication ordering.
- `plugins/agent-workflows/skills/goal-review/SKILL.md` owns non-interactive Codex review target selection and the review/fix loop.
- Repository `marketplace-tr-priority`, `.agents/skills/ozon-dimension-reference/**` remains the sole project-local owner of the Ozon package dimension and weight reference procedure, deterministic tool, tests, and conditional domain reference.

## Verified Current State

- The previous skill-architecture goal is complete, so this follow-up uses a new retained task pair.
- `.spec` lifecycle and layout are already fully defined by `goal-brainstorm`, but equivalent normative text is copied into:
  - `agent-plugins/DESIGN.md` and `agent-plugins/AGENTS.md`;
  - `project-standards/AGENTS.md`;
  - `project-documentation-developer`;
  - `project-standard-audit`, its workspace inventory, and its tests;
  - root `AGENTS.md` files in multiple governed consumer repositories.
- Technical `.gitignore` entries and pytest collection exclusions refer to `.spec` operationally without defining its normative lifecycle.
- `project-standard-audit` currently combines standalone instruction classification with complete semantic audit activation and repeats phase contracts across its skill and both references.
- `git-commit` and `goal-review` repeat the same mandatory behavior across goal, input/output, hard-rule, workflow, verification, and anti-pattern sections.
- `ozon-dimension-reference` is a coherent 117-line project-local skill; its mechanical validation details duplicate its deterministic Pydantic validator and generated schema, while routine `status` and mechanical `validate` do not need the complete evidence and formula contract.
- Repository `marketplace-tools` contains pre-existing user changes. They must be preserved and must not be rewritten as part of this ownership cleanup.

## Approved Decisions

### Exclusive `.spec` Ownership

- `.spec` belongs normatively only to `agent-workflows:goal-brainstorm`.
- Stable design, project instructions, project documentation standards, generic standards audits, and consumer projects must not copy `.spec` layout, lifecycle, retention, deletion, ignore, or coordinating-repository rules.
- Provider and project architecture may identify `goal-brainstorm` as the sole owner without restating its contract.
- Technical implementation references remain where required:
  - root `.gitignore` entries continue to ignore `.spec`;
  - pytest collection continues to exclude `.spec`;
  - `goal-brainstorm` uses exact Git behavior to verify ignore and tracking state.
- Existing `.spec` files are preserved. This task authorizes no task-artifact deletion.

### `project-standard-audit`

- Its discovery description triggers only for a complete semantic audit of one governed repository or an explicit workspace.
- Standalone instruction ownership classification belongs to `project-instruction-developer`; a full audit may still apply that owner while evaluating classifications.
- `SKILL.md` remains the orchestration owner for the mechanical phase, independently derived semantic phase, fix cycle, and handoff.
- `references/workspace-inventory.md` owns only deterministic repository discovery and mechanical inventory.
- `references/project-standard-audit.md` owns only the complete semantic audit.
- The workspace inventory no longer hard-codes `.spec`, exposes `task_root_issue_list`, or tests task-artifact policy.
- Audit depth remains unchanged: every applicable requirement receives evidence and a verdict, every inapplicable capability receives a reason, and each fix invalidates the prior semantic completion pass.

### `git-commit`

- Preserve the complete visible-change-set default, explicit exclusions, logical commit partitioning, separate governance and Product commits, default push behavior, and submodule-before-superproject ordering.
- Replace repeated sections with one commit contract, one workflow, and one handoff contract.
- Trigger information lives in frontmatter rather than a repeated `When To Use` body section.

### `goal-review`

- Preserve non-interactive `codex review`, exact target selection, clean branch-relative review, review findings as failure, required fix verification, `git-commit` integration, and repetition until one fresh review is clean.
- Keep one short boundary, the target-selection algorithm, one review loop, and one handoff contract.
- Remove inverse anti-patterns and repeated hard-rule and verification prose.

### `ozon-dimension-reference`

- Keep the skill and all owner-local assets in `marketplace-tr-priority`.
- Keep mode selection, deterministic tool commands, mutation workflow, and handoff in `SKILL.md`.
- Add one directly linked `references/dimension-reference-contract.md` for reference-file roles, mapping inputs, public evidence, confidence, semantic readiness, and formula semantics.
- `status` loads no domain reference.
- Mechanical `validate` runs the tool first; the reference is required only to interpret failures beyond mechanics or claim semantic production readiness.
- Creating, synchronizing, or changing reference data requires the complete domain reference.
- Mechanical field, range, uniqueness, cross-file, and coverage validation remains owned by the deterministic tool and generated schema rather than a copied prose checklist.

## Source-To-Target Ledger

| Source | Approved target |
| --- | --- |
| `.spec` owner bullets and `Harness-neutral task artifacts` in `agent-plugins/DESIGN.md` | Sole normative owner `agent-workflows:goal-brainstorm`; retain only provider-assignment wording in stable design |
| Task-pair lifecycle prose in `agent-plugins/AGENTS.md` | Removal; `Required Workflows` already binds `goal-brainstorm` |
| `.spec` project contract, tree node, and path bullet in `project-standards/AGENTS.md` | Removal |
| `.spec` routing and lifecycle in `project-documentation-developer/SKILL.md` and references | Removal; the skill retains only stable design and maintained-documentation ownership |
| Task-artifact trigger and repeated `.spec` audit rules in `project-standard-audit` | Removal; task artifacts remain under `goal-brainstorm` |
| `_task_root_issue_list_get`, `task_root_issue_list`, and owner-local tests in workspace inventory | Removal |
| Copied active-task-pair statements in governed consumer `AGENTS.md` files | Removal without replacement prose |
| Standalone classification trigger in `project-standard-audit` | `project-instruction-developer`; complete standards audits retain their own exhaustive classification phase |
| Repeated commit semantics in `git-commit` sections | One same-skill `Commit Contract`, `Workflow`, and `Handoff` |
| Repeated review semantics in `goal-review` sections | One same-skill `Boundary`, `Target Selection`, `Review Loop`, and `Handoff` |
| Detailed domain and duplicated mechanical validation prose in `ozon-dimension-reference/SKILL.md` | One conditional domain reference; exact mechanics remain in the tool and generated schema |

The copied consumer instruction removal applies to repositories `compose-mysql`, `marketplace-infrastructure`, `browser-runtime`, `v0-arasta-link`, `workflow-container-runtime`, `brand-size-chart`, `marketplace-parser`, `arasta-link-docs`, `workflow-container-contract`, `marketplace-next-co-uk`, `marketplace-tr`, `compose-milvus`, `marketplace-tools`, `vpn-runtime`, and `scrapy-next-deprecated`.

## Compatibility And Publication

- No skill identity, plugin identity, command, JSON shape, or runtime formula changes.
- No forwarding reference, compatibility alias, or duplicate `.spec` owner remains.
- Governed consumers declare the canonical `project-standards` Git dependency without a revision suffix; a commit, tag, or branch may be added only when the user explicitly authorizes it.
- The current `vpn-runtime` source commit is published before any `marketplace-tools` gitlink update that is required to consume its corrected instructions.
- Provider source and installed plugin cache must match the final published revisions before fresh-session acceptance.
- Pre-existing unrelated user changes remain intact; task commits must not rewrite or discard them.

## Verification And Acceptance

Apply every affected repository's current `AGENTS.md` verification contract.

Executable verification must include:

- project-standards workspace-inventory tests for clean catalog inventory, catalog failures, missing root instructions, and duplicate worktrees after removal of task-root output;
- standalone `ozon-dimension-reference` tool tests;
- provider and affected consumer pytest suites required by their changed executable scope;
- formatting and direct CLI behavior for changed Python code.

Skill and instruction verification must include:

- official plugin validation for every changed plugin;
- official quick validation for every changed skill;
- deterministic listing and target-model execution of every changed behavior corpus;
- explicit coverage that standalone instruction classification does not activate the full standards audit;
- explicit coverage that `.spec` creation or audit activates `goal-brainstorm`;
- unchanged semantic outcomes for commit publication ordering, Codex review looping, and Ozon reference ownership/evidence;
- full mechanical checks for all governed repositories after consumer dependency propagation;
- semantic reread of every changed instruction, reference, stable design owner, and consumer boundary;
- a fresh plugin-discovery session after installing the final provider source.

After presumed completion, repeatedly perform `Terminal Completion Audit` from the paired goal against this specification and every stable source owner. Each finding requires a fix, affected verification, and a new full audit from owner discovery. Completion requires one fresh zero-finding pass, preserved user changes, logical commits, successful pushes, and upstream equality for every task-owned repository.
