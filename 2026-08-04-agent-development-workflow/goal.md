# Реализовать Замкнутый Локальный Linear Workflow Разработки

## Outcome

Реализовать единый локальный workflow, в котором `goal-brainstorm` публикует свободно пересматриваемые source contracts в `project-goals`, а после подтверждённого handoff весь task graph и его lifecycle принадлежат Linear. Пользователь должен иметь возможность без Symphony и AWS пройти полный цикл следующей задачи: graph publication, fresh-thread implementation, tests, commit, push, PR, CI, Human Review, Rework, Merging, post-merge review, acceptance, cleanup и Project completion.

Одновременно создать independently installable `linear-agent-tools`, удалить superseded executable goal lifecycle после завершения transition и зафиксировать в `development-infrastructure` только стабильный design будущих Symphony control plane и Codex worker pool в AWS account `227373271916`.

## Source Contracts

- `spec.md`: полный task-specific implementation и acceptance contract.
- `agent-plugins/DESIGN.md`, раздел `Goal Brainstorm И Linear Task Workflow`: stable owner authoring, Linear graph, task lifecycle, workspace и verification semantics.
- `project-goals/DESIGN.md`: stable owner source artifact и Linear handoff boundary.
- `development-infrastructure/DESIGN.md`: stable owner будущей developer infrastructure и границы следующей Symphony/AWS specification.

## Constraints

- Implementation repositories: `agent-plugins` и `development-infrastructure`; `project-goals` изменяется только через approved serialized direct-main authoring transaction.
- Не реализовывать Symphony runtime, remote worker orchestration или AWS resources и не изменять `workflow-control-center`.
- После transition не оставлять checkpoint/merge/delete compatibility lifecycle, parallel task graph, spec sealing, persistent execution goal или dual-write между Linear и `project-goals`.
- Linear issue является canonical per-task goal; Git/GitHub остаются владельцами branches, commits, PR и checks, а Linear хранит их task-lifecycle links.
- Любая agent attempt использует fresh Codex thread и восстанавливает context только из Linear, exact source contracts, Git/GitHub и bounded local crash-recovery state.

## Verification

- Выполнить real contract probes official Linear MCP, required GraphQL gaps, issue/Project statuses, labels, documents, blockers и GitHub linking до bulk implementation.
- Пройти plugin/skill validators, полный `agent-plugins` pytest, focused graph/workspace/recovery/receipt/cleanup tests и behavior evaluation на target model.
- В exact bound Linear account и team из specification создать bounded isolated acceptance Project family и доказать полный manual local workflow без Symphony, включая authenticated-destination guard, configuration reconciliation, interrupted import, one-transition activation, blocked `Todo`, Rework, exact candidate approval, GitHub linking, merge, remediation review loop, final acceptance, canceled cleanup и Project completion.
- После последнего fix заново выполнить полный semantic and external-state audit всего scope; завершить goal только после свежего прохода без findings и опубликованных closing commits всех participants.
