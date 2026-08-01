# Реализовать Укрепление Release, Development Environment И VPN Runtime

## Outcome

Полностью реализовать paired specification `.spec/2026-08-01-workflow-platform-hardening-spec.md` и получить единственную current pre-production implementation в `agent-plugins`, `project-goals`, `project-standards`, `workflow-infrastructure`, `workflow-control-center` и `vpn-runtime`.

Результат обязан одновременно:

- сделать Product и WorkflowSource OCI publication crash-safe до первого registry PUT, продолжать exact retained bytes и удалять полный недостижимый root/runnable/attestation manifest graph до Distribution blob GC;
- закрывать root-snapshot race durable registry-maintenance admission fence и возобновлять exact GC generation после crash;
- заменить два God-object contour на утверждённые cohesive packages без flat compatibility wrappers, mixins и второго OCI implementation;
- оставить EC2 UserData только AMI-supported SSM-agent prerequisite, запускать content-addressed Python bootstrap через exact numeric/hash-bound SSM Command document и разделить Python bootstrap по утверждённым владельцам;
- создать единственного account-foundation owner, primary-only AWS Backup и корректную Session Manager observability boundary;
- удалить public VPN validation endpoint и `observed_exit_ip`, проверять tunnel через private S3 nonce без AWS credentials в validation Pod;
- реализовать stable fail-closed proxy, generation/Pod/runtime-instance fencing, spare/no-spare slot rotation и удалить весь WorkflowRun pause/freeze/VPN-replacement contour;
- ожидать simultaneous readiness всех присоединённых VPN до первой activation WorkflowRun и после activation не связывать VPN health/rotation с WorkflowRun lifecycle;
- материализовать per-`VpnConfigVersion` timing contract, провести real ARM64/k3s `/dev/net/tun` measurements и принять defaults/ranges с доказанным запасом;
- предоставлять одну полную isolated development environment на exact task common prefix, использовать только `git-worktree=<common-prefix>` для task tag и не добавлять project name в development-account resource identities;
- централизовать future task artifacts в tracked `project-goals/main` без coordination task branches, linked worktrees и bootstrap manifest, реализовать separate `goal-checkpoint`, exclusive `goal-merge` and `goal-delete`, сохранить ordinary pre-activation revision и удалить target project-local `.spec` protocol;
- реализовать YAML schema-v2 project cleanup hook, content-free self-hosting binding receipt и единую resumable external-resources → worktrees → refs → task-directory cleanup transaction без historical harness-goal dependency;
- применить shared YAML-by-default contract, заменить implementation-repository `worktree-bootstrap.toml` на `.yaml`, не публиковать bootstrap manifest в `project-goals/main` и заменить WCC `.yml` bootstrap template на `.yaml`, сохранив idiomatic JSON/TOML owners;
- удалить перечисленные pre-production legacy resources, state shapes и compatibility branches, сохранив generic database migration, domain versioning, rollback и recovery mechanisms текущей системы.

## Source Contracts

Полным implementation contract являются paired specification и все stable owners, перечисленные в её разделах `Назначение`, `Владение Репозиториями` и `Verification Design`, включая:

- `agent-plugins/DESIGN.md`;
- `agent-plugins/plugins/agent-workflows/skills/goal-brainstorm/SKILL.md`;
- `agent-plugins/plugins/agent-workflows/skills/goal-brainstorm/references/specification-contract.md`;
- `agent-plugins/plugins/agent-workflows/skills/goal-brainstorm/references/worktree-contract.md`;
- `project-goals/DESIGN.md`;
- `project-standards/plugins/project-standards/skills/project-foundation/references/machine-readable-format.md`;
- `workflow-infrastructure/design/environment-model.md`;
- `workflow-infrastructure/design/development-environment.md`;
- `workflow-infrastructure/design/production-environment.md`;
- `workflow-infrastructure/docs/development-environment-operations.md`;
- `workflow-control-center/AGENTS.md` and every changed owner under `workflow-control-center/design/**` and `workflow-control-center/docs/**`;
- `vpn-runtime/DESIGN.md` and `vpn-runtime/README.md`.

При расхождении более узкий stable owner из paired specification уточняет общий contract; goal file не является отдельным owner durable architecture.

## Task Identity

```text
Specification: .spec/2026-08-01-workflow-platform-hardening-spec.md (self-hosting bootstrap carrier)
Goal: .spec/2026-08-01-workflow-platform-hardening-goal.md (self-hosting bootstrap carrier)
Target coordination directory: /home/andrey/Projects/project-goals/2026-08-01-workflow-platform-hardening
Branch: 2026-08-01-workflow-platform-hardening
Task roots:
- /home/andrey/Projects/agent-plugins/.worktree/2026-08-01-workflow-platform-hardening
- /home/andrey/Projects/project-goals/.worktree/2026-08-01-workflow-platform-hardening
- /home/andrey/Projects/project-standards/.worktree/2026-08-01-workflow-platform-hardening
- /home/andrey/Projects/workflow-infrastructure/.worktree/2026-08-01-workflow-platform-hardening
- /home/andrey/Projects/workflow-control-center/.worktree/2026-08-01-workflow-platform-hardening
- /home/andrey/Projects/vpn-runtime/.worktree/2026-08-01-workflow-platform-hardening
Specification links: every currently prepared task-root .spec is one relative bootstrap link to the coordinating main worktree's physical .spec/ directory; implementation removes this carrier dependency after publishing the exact central copy.
Execution boundary: every subsequent repository command stays in the exact task roots above until an explicitly authorized merge.
```

## Execution Constraints

- Выполнять repository reads, writes, tests, commits и pushes только в exact task roots из `Task Identity`; physical pair изменяется только через recorded task-worktree `.spec` link. Единственное исключение — exact bootstrap publication repository source contracts и central task directory в `project-goals/main`, явно определённая paired specification.
- Сохранить unrelated user state. Не переносить task-owned tracked changes в implementation main worktrees до отдельно разрешённого merge; coordination-only bootstrap exception выше не является Product-source merge.
- Не создавать и не изменять production AWS resources, Amazon EKS либо parallel production implementation. Реализовать общую environment-neutral часть, development specialization и только stable production design.
- В development account `463564115167` разрешены необходимые task changes без отдельной approval pause; перед первой task-scoped AWS mutation сначала реализовать, проверить и bind-ить sealed schema-v2 cleanup declaration и exact content-free receipt.
- Не использовать прямой Product `buildx --push`, mutable dependency identity, rebuild как crash recovery, duplicate OCI protocol, compatibility aliases или старые technical contract readers.
- Не возвращать pause-agent, process namespace sharing, signals-based freeze, VPN-triggered WorkflowRun execution/state transitions, public validation API или exit-IP reporting.
- Не угадывать final timeout bounds: измерить exact target runtime, зафиксировать accepted values во всех stable/API/ORM/runtime/test owners и повторить acceptance с ними.
- Не создавать отдельные completion evidence documents. Current self-hosting `.spec` pair сохраняется по activation path до будущего explicit `goal-delete`; central task history сохраняется в `project-goals`.
- Выполнять эту exact bootstrap goal по старому контракту через physical `.spec` pair и все шесть task roots из `Task Identity`, включая prepared `project-goals` worktree. Только следующие goals используют direct `project-goals/main` без coordination branch, worktree либо manifest.
- После implementation и verification создать логические commits, push всех participating task branches, доказать upstream equality и опубликовать first full checkpoint. Merge/primary acceptance и synchronized deletion остаются отдельными явно запускаемыми lifecycle actions.

## Verification And Completion

Выполнить весь applicable verification contract paired specification, stable owners и repository instructions. Как минимум:

1. `agent-plugins`: validators, полный provider pytest, behavior corpus и real temporary-Git tests всех revision/checkpoint/merge/delete/schema-v2/binding/crash boundaries.
2. `project-goals`: semantic contract audit, direct-main transaction and absence of target coordination worktree/branch/manifest, strict checkpoint YAML parsing, complete snapshot, accepted-pointer, concurrent publication and deletion-history acceptance.
3. `project-standards`: plugin/skill validators, full pytest, behavior corpus and semantic verification of `Machine-Readable Format Contract` ownership.
4. `workflow-infrastructure`: полный Python verification, CloudFormation validation/change-set inspection, exact account-foundation/SSM/bootstrap checks и real AWS task-environment acceptance без production mutation.
5. `workflow-control-center`: backend/ORM/controller/Kubernetes/release/retention tests, crash injection каждой OCI publication/deletion boundary, concurrent registry-root admission tests и применимый UI contract. Любое изменение `ui/**` выполняет exact handoff sequence из `AGENTS.md` против endpoint этой task environment.
6. `vpn-runtime`: полный pytest/compile/container verification и repeated real `/dev/net/tun` ARM64 measurements для connection, recovery, attempt replacement, stable-proxy switch/restart и graceful/forced process stop.
7. Cross-repository acceptance: exact source commits, full checkpoint publication, resumable one-checkpoint merge behavior, task environment identity/tag/isolation, initial all-VPN barrier, post-activation VPN independence, private nonce validation, crash recovery, mark-and-sweep, volume warnings, account-global ownership and synchronized-delete dry-run/inventory behavior.

После предполагаемого завершения начать полный semantic and external-state audit всего scope заново. Исправить каждую новую незавершённую, противоречивую, хрупкую, небезопасную, legacy, дублирующую либо непроверенную находку, повторить затронутые проверки и снова начать полный audit. Завершить goal только после свежего post-fix полного audit без findings, полной зелёной acceptance и доказанных commits/pushes всех participating repositories.
