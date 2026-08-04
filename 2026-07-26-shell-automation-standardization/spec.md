# Shell Automation Standardization

## Required Outcome

`project-standards:project-foundation` becomes the universal owner of the prohibition on project-local shell-script artifacts and of the exact mechanical `.sh` checker. `project-standards:python-cli-developer` owns only the positive contract for executable Python scripts and the migration of useful shell-owned behavior into Python.

Every current project-local `.sh` file in the governed top-level repositories under the approved scope is removed from the final state. Thin wrappers, unused setup artifacts, and generated helper scripts are deleted rather than translated into equally thin Python wrappers. Stable operational or domain behavior is preserved through a direct Python CLI owned by the affected project. No extension rename, inline shell module, compatibility wrapper, or line-for-line shell translation remains.

The final implementation handoff must list every deleted `.sh` file separately from every `.sh` file replaced by Python and identify the replacement command for each migrated file.

## Coordinating Repository And Document Mode

The coordinating repository is `project-standards` because it owns the universal executable-automation contract and the shared exact-checker runtime.

This pair uses a dedicated implementation specification because the task changes one provider-owned baseline standard and migrates 44 tracked files across ten consumer repositories with distinct operational, generated, legacy, infrastructure, and domain behavior.

The paired task artifacts are:

- `.spec/2026-07-26-shell-automation-standardization-spec.md`;
- `.spec/2026-07-26-shell-automation-standardization-goal.md`.

They remain ignored and untracked through the coordinating repository root `.gitignore` and are never deleted without an explicit user request.

## Verified Current State

- The previous persistent goal is complete; no unfinished goal owns this task pair.
- The ten governed top-level consumer repositories contain 44 tracked `.sh` paths with 18 unique file bodies.
- `project-standards:python-cli-developer` currently owns both the positive Python CLI contract and the `.sh` prohibition.
- The exact checker is selected only when a consumer declares `project-standards:python-cli-developer`. `compose-milvus`, `compose-mysql`, and `marketplace-infrastructure` contain tracked `.sh` paths but do not select that skill, so the current checker does not provide a universal baseline prohibition.
- The current shell inventory contains:
  - 18 thin duplicated Docker Compose, Git, and MySQL wrappers;
  - 18 environment, installation, deploy-key, or host-bootstrap scripts;
  - six operational or domain scripts;
  - two generated OpenAPI `git_push.sh` files.
- `marketplace-tools` has unrelated pre-existing local changes. They belong to the user and must be preserved without inclusion in this task's commits.
- Both generated Yandex API clients list `git_push.sh` in `.openapi-generator/FILES` and have an existing `.openapi-generator-ignore`, but neither repository tracks one canonical client-regeneration entrypoint.
- The ignored nested GlitchTip research checkout under `workflow-control-center/tmp/**` is an independent external research repository and is outside this task.

## Approved Standard Contract

The durable owner is `plugins/project-standards/skills/project-foundation/references/repository-model.md`, section `Executable Automation Contract`:

- every non-ignored project-local path whose name ends in `.sh` is forbidden;
- standalone non-ignored project-local executable automation cannot be implemented in a shell language and uses a `Python script` when a standalone script is required;
- executable shell-language code is allowed only as boundary-local command text inside an externally owned configuration or runtime command field, stays limited to the minimum adaptation required by that boundary, and cannot own reusable project automation or project policy.

No line-count limit, path allowlist, permitted-name list, content threshold, or general shell-script exception exists. If an external protocol ever literally requires a standalone shell-script path, that is a new explicit user-authorized project specialization rather than an inferred exception to this task.

The verification boundary is intentionally split:

- the non-ignored `*.sh` path prohibition is one independently normative closed predicate and remains an exact mechanical checker;
- extensionless shell artifacts, shell hidden under another suffix, and embedded command text that has become reusable logic or orchestration are semantic requirements and receive complete semantic audit rather than an approximate content scanner;
- checker success never replaces or narrows semantic review.

The exact checker, manifest, and owner-local behavior tests move from `project-standards:python-cli-developer` to `project-standards:project-foundation`. The checker identity becomes `project-foundation.repository-shell-script`. The old owner path, manifest entry, checker identity, and duplicate test path are removed without a compatibility alias.

## Scope

The task includes:

- provider contract, routing, checker ownership, packaged assets, tests, design examples, and repository-reference fallout;
- deletion or Python migration of every tracked `.sh` path listed below;
- direct caller, instruction, design, README, deployment, generated-code, and operational reference updates;
- behavior tests and direct `--help` verification for every new Python CLI;
- source-only prerequisite updates required to launch one replacement Python CLI, including explicit Python 3 availability in the production marketplace replacement-host `CloudFormation UserData`;
- removal of empty script directories when they have no remaining owner;
- complete mechanical and semantic acceptance in every affected repository;
- logical commits and pushes for every changed tracked repository;
- one final handoff inventory split into deleted and Python-migrated paths.

The task does not include:

- changing product business semantics, external API behavior, persisted data, database schemas, live production service state, live AWS resource state, or production credentials;
- executing package installation, host bootstrap, model training, monitoring, database readiness, Git remote mutation, or generated-client publication against production;
- retaining shell wrappers for command compatibility;
- creating Python wrappers whose only behavior is forwarding unchanged arguments to one command;
- moving project-specific operational logic into `project-standards`;
- modifying or committing unrelated pre-existing changes;
- deleting any `.spec` file.

## Complete File Disposition

### Delete Without Python Replacement

These 31 files do not own a stable standalone behavior that justifies a Python wrapper:

- repository `compose-milvus`:
  - `bin/deploy_keys_create.sh`;
  - `bin/docker_compose_rebuild.sh`;
  - `bin/git_pull.sh`;
  - `bin/initial_install.sh`;
  - `bin/mysql.sh`;
  - `bin/venv_create.sh`;
- repository `compose-mysql`:
  - `bin/deploy_keys_create.sh`;
  - `bin/docker_compose_rebuild.sh`;
  - `bin/git_pull.sh`;
  - `bin/initial_install.sh`;
  - `bin/mysql.sh`;
  - `bin/venv_create.sh`;
- repository `marketplace-next-co-uk`:
  - `bin/deploy_keys_create.sh`;
  - `bin/docker_compose_rebuild.sh`;
  - `bin/git_pull.sh`;
  - `bin/initial_install.sh`;
  - `bin/mysql.sh`;
- repository `marketplace-parser`:
  - `bin/docker_compose_rebuild.sh`;
  - `bin/git_pull.sh`;
  - `bin/initial_install.sh`;
  - `bin/mysql.sh`;
- repository `marketplace-tools`:
  - `bin/docker_compose_rebuild.sh`;
  - `bin/git_pull.sh`;
  - `bin/initial_install.sh`;
  - `bin/mysql.sh`;
  - `yandex_agent/generated/git_push.sh`;
- repository `marketplace-tr`:
  - `bin/docker_compose_rebuild.sh`;
  - `bin/git_pull.sh`;
  - `bin/initial_install.sh`;
  - `bin/mysql.sh`;
  - `yandex_agent/generated/git_push.sh`.

The Docker Compose, Git, and MySQL wrappers are replaced at real use sites by their direct canonical command, not by another wrapper. The old deploy-key and installation scripts have no tracked caller or script-specific maintained behavior contract and are deleted. The two generated Git scripts are added to the respective `.openapi-generator-ignore` files with the root-relative `/git_push.sh` pattern and removed from `.openapi-generator/FILES` so the maintained generated-client metadata no longer declares them and future regeneration does not restore them.

### Replace With Python

These 13 files own useful environment, operational, infrastructure, document-conversion, or model-pipeline behavior and must be replaced by directly executable Python CLIs:

- repository `arasta-link-docs`:
  - `scripts/convert_md_to_docx.sh`;
- repository `marketplace-infrastructure`:
  - `host/bootstrap-host.sh`;
- repository `marketplace-next-co-uk`:
  - `bin/venv_create.sh`;
- repository `marketplace-parser`:
  - `bin/venv_create.sh`;
- repository `marketplace-tools`:
  - `bin/venv_create.sh`;
  - `deploy/host/marketplace-resource-monitor.sh`;
  - `deploy/host/marketplace-scheduled-job-watch.sh`;
  - `deploy/host/marketplace-wait-for-databases.sh`;
  - `ml/offer_priority/main/train.sh`;
- repository `marketplace-tr`:
  - `bin/venv_create.sh`;
  - `ml/offer_priority/main/train.sh`;
- repository `scrapy-next-deprecated`:
  - `create_venv.sh`;
- repository `v0-arasta-link`:
  - `bootstrap.sh`.

The approved replacement commands are:

| Repository | Current path | Replacement command |
| --- | --- | --- |
| `arasta-link-docs` | `scripts/convert_md_to_docx.sh` | `scripts/markdown_docx_convert.py` |
| `marketplace-infrastructure` | `host/bootstrap-host.sh` | `host_bootstrap.py` |
| `marketplace-next-co-uk` | `bin/venv_create.sh` | `tool/venv_create.py` |
| `marketplace-parser` | `bin/venv_create.sh` | `tool/venv_create.py` |
| `marketplace-tools` | `bin/venv_create.sh` | `tool/venv_create.py` |
| `marketplace-tools` | `deploy/host/marketplace-resource-monitor.sh` | `deploy/host/marketplace_resource_monitor.py` |
| `marketplace-tools` | `deploy/host/marketplace-scheduled-job-watch.sh` | `deploy/host/marketplace_scheduled_job_watch.py` |
| `marketplace-tools` | `deploy/host/marketplace-wait-for-databases.sh` | `deploy/host/marketplace_database_readiness_wait.py` |
| `marketplace-tools` | `ml/offer_priority/main/train.sh` | `ml/offer_priority/main/offer_priority_model_train.py` |
| `marketplace-tr` | `bin/venv_create.sh` | `tool/venv_create.py` |
| `marketplace-tr` | `ml/offer_priority/main/train.sh` | `ml/offer_priority/main/offer_priority_model_train.py` |
| `scrapy-next-deprecated` | `create_venv.sh` | `tool/venv_create.py` |
| `v0-arasta-link` | `bootstrap.sh` | `tool/host_bootstrap.py` |

Each replacement path is one directly executable `Python script`. The migration updates every real caller and retained command reference atomically and leaves no forwarding alias at the old path.

After the approved deletions and moves, `bin/` has no remaining tracked owner in `compose-milvus`, `compose-mysql`, `marketplace-next-co-uk`, `marketplace-parser`, `marketplace-tools`, or `marketplace-tr`; those six directories are removed, and their instruction, documentation, and command references are deleted or updated to the actual new owner. After `marketplace-infrastructure/host/bootstrap-host.sh` moves to the approved root command, its empty `host/` directory is removed and `host_bootstrap.py` is mapped explicitly in the repository instructions and maintained design.

## Preserved Behavior By Python Migration Family

### Markdown To DOCX

The `arasta-link-docs` replacement preserves:

- one Markdown file or one directory as input;
- optional output directory with the current default;
- deterministic top-level Markdown file ordering for directory input;
- optional `scripts/reference.docx`;
- the current Pandoc input extensions, DOCX output, language, resource path, wrapping, and DPI options;
- concrete failures for missing Pandoc, invalid argument count, missing input, and an empty source directory;
- one success record per converted file.

### Environment Creation

Each environment-creation replacement is a narrowly owned environment tool, not a host bootstrap. It resolves the repository root from `tool/venv_create.py`; selects `python3.14` by default while preserving the existing `PYTHON_BINARY` environment override; requires the Python 3.14 minor line without pinning one patch release; validates the selected interpreter before mutation; recreates root `venv/` through `python -m venv --clear`; and installs only that repository's canonical Python dependency inputs through `venv/bin/python -m pip`. It uses argument-vector subprocess execution without shell activation.

The environment tool does not run `apt update`, `apt upgrade`, `apt full-upgrade`, `apt install`, `sudo`, `chown`, unconditional upgrades of `pip`, `setuptools`, or `wheel`, or unrelated directory creation. Required operating-system packages belong to the maintained host prerequisite or bootstrap boundary; a missing prerequisite produces the concrete dependency-installation failure instead of causing the venv tool to mutate the host.

The `marketplace-next-co-uk` replacement preserves its hash-locked `requirements-dev.lock` installation. The `marketplace-parser` replacement preserves its `requirements.txt` installation. The `marketplace-tools` and `marketplace-tr` replacements preserve their root requirements plus editable installation of `yandex_agent/generated` without dependency resolution. The retained deprecated Scrapy environment replacement installs only its project requirements; its required operating-system packages are stated as host prerequisites in maintained documentation rather than installed by the tool.

### Host Bootstrap

The infrastructure and V0 Arasta Link replacements preserve their current platform, architecture, privilege, package-source, package-installation, service, user-group, directory, and final-version reporting behavior. They use explicit argument vectors and direct file APIs rather than shell pipelines or sourced environment files.

The `marketplace-infrastructure` replacement remains the software bootstrap owner for a clean Ubuntu 24.04 ARM64 replacement host and remains separate from CloudFormation resource ownership. The tracked `CloudFormation UserData` explicitly installs Python 3 as the minimal external-boundary prerequisite for that replacement command. This task validates the template but neither applies its stack nor executes the mutating bootstrap on a live host.

The `v0-arasta-link` replacement preserves Ubuntu validation, root escalation behavior, Docker repository installation, Docker service enablement, project directory creation, Docker-group membership, and final version reporting. Its verification does not mutate the current development host.

### Marketplace Operational Tools

The `marketplace-tools` resource monitor replacement preserves the current environment-backed interval, duration, output directory, TSV schemas, metadata, host metrics, cgroup metrics, MySQL metrics, ClickHouse metrics, error capture, and bounded completion marker.

The scheduled-job watcher replacement preserves its monitor metadata boundary, log start-time filtering, workload success counters, throttled error scanning, Docker restart and OOM counts, zombie count, changed-state output, service-active polling, and final snapshot.

The database-readiness replacement preserves the bounded MySQL and ClickHouse readiness condition, current attempt and timeout semantics, successful zero exit, and concrete final failure.

### Offer Priority Training

Both model-training replacements preserve the existing three named modes, the no-mode dataset-only behavior, all current date ranges, generated datasets, model filenames, snapshot cleanup, iteration counts, evaluation steps, and feature-importance steps. Unknown modes remain a concrete CLI error. Shared stable behavior within one repository has one owner and is not duplicated across helper-only Python wrappers.

## Migration And Compatibility

This is one coordinated steady-state migration with no compatibility period.

- Provider ownership and packaged checker support are published before consumer acceptance relies on the new baseline checker.
- A consumer removes one `.sh` file only in the same change that removes or migrates every real caller and maintained reference.
- Converted CLIs preserve semantic inputs, outputs, environment variables, exit status, side-effect ordering, and failure behavior unless the approved contract above explicitly removes host mutation or unrelated setup from environment creation, strengthens validation, or adds the required `--help`.
- Trivial wrappers disappear; their historical filenames and invocation shapes are not compatibility contracts.
- Generated OpenAPI clients retain their generated Python code while their ignore files prevent future `git_push.sh` emission.
- No `.sh` file may be renamed to another suffix or made extensionless.
- Shell command blocks already embedded in external configuration are not automatically rewritten, but the terminal semantic audit must verify that they stay limited to minimum boundary adaptation and do not form a second project-automation or project-policy owner.
- Consumer instructions and maintained documentation are updated only with the corresponding code change so they always describe the current checked-in state.
- Unrelated dirty files in `marketplace-tools` remain untouched and outside task commits.

## Verification Design

### Provider Standard And Checker

Provider automated tests must prove:

- a consumer selecting only mandatory `project-standards:project-foundation` receives the `.sh` checker;
- every in-scope root or nested `.sh` path, including a symlink path, produces one deterministic finding;
- a scope without `.sh` paths is clean;
- `changed` and `all` runner scopes execute the moved checker through the same installed-distribution path;
- the old `python-cli.repository-shell-script` identity and old owner assets are absent;
- the wheel contains exactly one current manifest, checker script, and behavior-test owner path for the rule;
- checker output remains mechanical evidence and `semantic_audit_required=true`.

Provider verification includes plugin validation, every skill validation, targeted checker and distribution tests, the complete provider `pytest -q` suite, `project-standard-check --project-root . --scope all`, and complete semantic review of every applicable provider contract.

### Deleted Scripts

For every deletion:

- Git and non-ignored workspace scope contain no old path;
- no instruction, documentation, code, deployment artifact, generated configuration, or maintained command references the deleted filename;
- a direct command replaces a retained operational instruction only when that command represents real current behavior;
- each deleted generated Git script is absent, is ignored by the exact root-relative `/git_push.sh` generator pattern, and is absent from `.openapi-generator/FILES`; because no canonical regeneration entrypoint is tracked, this exact generated metadata is the current verifiable generator boundary;
- no empty directory or compatibility wrapper remains.

### Converted Python CLIs

Every replacement follows the applicable `project-standards:python-developer`, `project-standards:python-cli-developer`, `project-standards:runtime-config-developer`, `project-standards:pytest-developer`, Legacy, deployment, and project-local contracts. Each consumer adds every newly applicable standard to `Required Standards` in the same change; in particular, a repository that receives its first governed Python CLI or owner-local behavior test cannot retain a shell-only or unmodeled instruction selection.

The current selection fallout is explicit:

- `marketplace-infrastructure` adds `project-standards:python-developer`, `project-standards:python-cli-developer`, and `project-standards:pytest-developer`;
- `arasta-link-docs` and `scrapy-next-deprecated` add `project-standards:pytest-developer`;
- `v0-arasta-link` adds `project-standards:pytest-developer` and `project-standards:project-documentation-developer`;
- `marketplace-next-co-uk` and `marketplace-tr` add `project-standards:project-documentation-developer` because their maintained installation documentation changes;
- every other affected consumer already selects the capabilities required by its planned artifact changes or loses the artifact that would otherwise trigger the capability.

Automated behavior tests for each replacement family must cover:

- the success path without production mutation;
- the primary contract-defining external-command, validation, or readiness failure;
- every critical branch introduced or preserved by the migration;
- argument parsing and `--help`;
- environment-backed configuration and secret-safe output where applicable;
- exact subprocess argument vectors, output artifacts, and exit status at the external boundary without asserting incidental private call order.

Environment-tool tests additionally prove the default `python3.14` selection, acceptance of another executable path only when it reports Python 3.14, rejection of every other minor version before environment mutation, project-root resolution independent of the caller's current working directory, exact canonical dependency inputs, and absence of operating-system package, privilege, packaging-tool upgrade, activation-shell, and unrelated-directory operations.

Direct verification runs each replacement's real `--help` path. Destructive or production-bound behavior is exercised through an isolated fake external-command boundary, never against production.

Each consumer runs its directly affected tests and applicable ordinary handoff suite. Compose consumers additionally run `docker compose config`; infrastructure validates its maintained design and CloudFormation artifacts without applying an AWS change; UI or product browser suites are not required because this task changes no browser-visible behavior.

### Workspace Acceptance

After all provider and consumer changes:

- the exact provider checker runs for every governed repository through mandatory `project-foundation`;
- no non-ignored governed project path ends in `.sh`;
- a complete semantic audit independently inspects every governed repository for extensionless shell scripts, alternate suffixes, and embedded shell logic that exceeds minimum boundary adaptation or owns reusable project automation or project policy;
- all ten affected repositories have current instructions and documentation with no obsolete shell validation or command references;
- every required test, build, direct CLI verification, and repository-specific handoff command passes;
- every changed tracked repository has all task-owned changes committed in logical commits and pushed with `HEAD` exactly equal to its upstream branch;
- every repository without unrelated pre-existing changes is clean, while `marketplace-tools` retains its unrelated pre-existing changes as the only allowed dirty state and reports them explicitly at handoff;
- the final handoff contains two exhaustive lists: deleted shell paths and shell paths replaced by Python, with each replacement command.

## Terminal Completion Audit

After presumed completion, audit the complete current scope from scratch against this specification, the paired goal, every referenced provider contract, all ten consumer repositories, current generated-code rules, current instructions and documentation, Git state, and upstream publication state.

If the audit finds any remaining `.sh` path, shell automation under another name, embedded second automation layer, stale reference, missing behavior branch, incomplete verification, lost operational behavior, unrelated included change, unpublished commit, or other unfinished requirement, keep the goal active, fix every finding, rerun affected verification, and start another complete audit from scratch. Repeat until one post-fix full audit finds none.
