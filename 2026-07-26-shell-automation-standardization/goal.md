# Shell Automation Standardization

## Outcome

Make `project-standards:project-foundation` the universal owner and exact mechanical enforcer of the project-local shell-automation prohibition; remove all 44 current tracked `.sh` files from the ten governed consumer repositories by deleting the 31 thin, unused, or generated artifacts and replacing the 13 behavior-owning scripts with direct tested Python CLIs that preserve their approved behavior.

## Source Contracts

- `.spec/2026-07-26-shell-automation-standardization-spec.md`: complete file disposition, preserved behavior, migration boundary, verification design, publication, and handoff contract.
- `AGENTS.md` and `DESIGN.md`: coordinating provider ownership, exact-checker architecture, validation, and publication boundary.
- `plugins/project-standards/skills/project-foundation/SKILL.md`: baseline routing for project-local executable automation.
- `plugins/project-standards/skills/project-foundation/references/repository-model.md`, section `Executable Automation Contract`: universal shell-artifact and executable-automation owner.
- `plugins/project-standards/skills/project-foundation/references/execution.md`: mutation safety, evidence, semantic audit, verification, and handoff integrity.
- `plugins/project-standards/skills/project-instruction-developer/references/skill-model.md`: exact-checker ownership and eligibility contract.
- `plugins/project-standards/skills/project-instruction-developer/references/repository-reference.md`: repository-local instruction-reference contract.
- `plugins/project-standards/skills/project-documentation-developer/references/documentation.md`: stable documentation and task-artifact ownership.
- `plugins/project-standards/skills/project-standardize/SKILL.md`: complete workspace discovery and separate mechanical inventory routing.
- `plugins/project-standards/skills/project-standard-audit/SKILL.md`: independent complete semantic acceptance and post-fix restart owner.
- `plugins/project-standards/skills/aws-cloudformation-developer/SKILL.md`: source-only replacement-host prerequisite and CloudFormation validation contract without live stack execution.
- `plugins/project-standards/skills/python-cli-developer/SKILL.md`, `references/python-script.md`, and `references/cli.md`: Python replacement, executable-file, CLI, environment, and direct-verification contracts.
- `plugins/project-standards/skills/python-developer/SKILL.md`: routing to applicable Python ownership, naming, structure, typing, validation, and formatting contracts.
- `plugins/project-standards/skills/runtime-config-developer/SKILL.md`: routing to applicable environment and secret configuration contracts for converted tools.
- `plugins/project-standards/skills/pytest-developer/SKILL.md`: routing to behavior-test placement and verification contracts.
- Root `AGENTS.md` and directly affected maintained documents in repositories `arasta-link-docs`, `compose-milvus`, `compose-mysql`, `marketplace-infrastructure`, `marketplace-next-co-uk`, `marketplace-parser`, `marketplace-tools`, `marketplace-tr`, `scrapy-next-deprecated`, and `v0-arasta-link`: consumer ownership, safety, current commands, and handoff overlays.

## Constraints

- Follow the complete 31-delete and 13-Python-migration disposition without shell aliases, extension renames, inline replacement modules, trivial Python forwarding wrappers, or compatibility bridges.
- Preserve every behavior explicitly owned by the paired specification and update all real callers, generated-code controls, instructions, and maintained documentation atomically.
- Put every retained environment creator at `tool/venv_create.py`; keep it limited to a validated Python 3.14 `venv` plus canonical project dependency installation, with no operating-system update or package installation, privilege mutation, shell activation, unconditional packaging-tool upgrade, or unrelated directory setup.
- Put the `marketplace-infrastructure` replacement at root `host_bootstrap.py`, remove the emptied legacy `host/` owner, and remove every emptied legacy `bin/` owner named by the paired specification.
- Keep project-specific runtime behavior with its consumer owner and generic policy/checker behavior with `project-standards`.
- Preserve unrelated user changes, especially the current dirty `marketplace-tools` files, and exclude them from task commits.
- Perform no production, AWS, database, marketplace, host-bootstrap, model-training, or external publication mutation beyond Git commits and pushes.
- Retain every `.spec` pair ignored and untracked.
- Commit logically and push every changed tracked repository.

## Verification

Satisfy the entire verification design in the paired specification and every applicable provider and consumer handoff contract. Prove baseline checker selection through `project-foundation`, exact `.sh` findings, absence of the old checker owner, preserved Python CLI behavior, generator stability, direct `--help`, consumer-specific suites, complete workspace mechanical and semantic conformance, clean scoped Git state, upstream equality, and exhaustive deleted-versus-migrated handoff reporting.

After presumed completion, repeatedly audit the complete current scope from scratch against this goal, the paired specification, and every source contract; fix every incomplete or contradictory finding, rerun affected verification, and start another complete audit until one post-fix audit finds none. Only then complete the goal.
