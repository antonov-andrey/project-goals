# Project Goals

## Назначение

`project-goals` является canonical tracked source repository для implementation goals до их отправки в Linear. Он хранит согласованный outcome и complete source specification, но не владеет operational task graph, implementation execution, Product code или deployment runtime.

## Source Artifact Model

Один exact common prefix создаёт один directory:

```text
<common-prefix>/
  goal.md
  spec.md
```

Common prefix является basename, содержит creation date и stable semantic name. Он идентифицирует source artifact, но не обязан совпадать с downstream Linear issue branch или workspace.

`goal.md` владеет concise outcome и exact source references. `spec.md` владеет complete implementation contract, architecture decisions, scope, failure behavior и verification obligations. Оба файла готовит `agent-workflows:goal-brainstorm`.

Repository использует только canonical checkout ветки `main`. Task branches, linked worktrees, bootstrap manifests, project-local `.spec/`, task-artifact symlinks и copies запрещены.

## Authoring

До Linear handoff `agent-workflows:goal-brainstorm` может свободно пересматривать тот же source directory после каждого нового решения пользователя. Internally consistent candidate не становится immutable state и не активирует persistent goal.

Каждая authoring mutation использует короткую serialized direct-main transaction. Transaction получает workspace-global lock, проверяет clean synchronized `main`, меняет только exact owned paths, создаёт ordinary commit и push без force, затем возвращает checkout в clean synchronized state.

Disjoint concurrent source changes могут быть повторены на новом remote tip. Concurrent change того же source directory требует semantic conflict resolution. Никакое согласованное изменение не остаётся только в working tree, temporary branch или unpushed commit.

## Linear Handoff

`linear-agent-tools:task-graph-create` получает exact full Git commit и root-relative `goal.md` и `spec.md`. Эти значения образуют immutable source snapshot независимо от будущего состояния `main`.

Skill показывает пользователю complete proposed DAG до mutation, затем создаёт один non-dispatchable Linear Project, issues и blocker relations. Он завершает handoff только после полного read-back proof и activation barrier. Partial import остаётся в Linear без dispatch permission и не создаёт tracked state в `project-goals`.

Source считается отправленным только после успешного Linear activation barrier. До этого пользователь может отменить partial import и продолжить ordinary authoring.

После handoff Linear становится единственным owner operational graph. Requirements, remediation, review, acceptance, rework и execution details меняются через Linear issues, relations, comments и statuses. `project-goals` не получает dispatch marker, task graph, checkpoint или execution journal.

Новый independent architecture brainstorm создаёт новый source directory. Он не переписывает exact Git snapshot уже отправленного source.

## Source-Independent Downstream

Linear task architecture не зависит от `project-goals`. Manual, incident, audit, monitoring и другие sources используют тот же `linear-agent-tools:task-graph-create` contract с собственными exact provenance values.

`project-goals` не определяет Linear statuses, labels, assignee policy, branch names, task roles или workspace lifecycle. Эти contracts принадлежат `linear-agent-tools` и Linear configuration.

## Historical Checkpoints

Existing directories могут содержать `checkpoint.yaml`, созданный superseded pre-Linear lifecycle. Эти files сохраняются как immutable historical data и Git evidence. Current workflow не создаёт, изменяет, принимает или удаляет их и не предоставляет executable compatibility для их прежней state machine.

Historical directory presence не создаёт task branch, worktree, AWS resource или cleanup obligation в current workflow. Уже существующие resources старой task очищаются transition owner до удаления old lifecycle skills.

## Verification

Authoring verification подтверждает:

- exact canonical repository and source directory identity;
- source coherence, complete decisions и отсутствующие open placeholders;
- clean serialized direct-main publication;
- отсутствие task graph, branch, worktree, bootstrap manifest и local task-artifact copies;
- exact Git commit identity, пригодную для Linear source permalink;
- semantic reread `goal.md`, `spec.md` и directly referenced stable owners after every correction.

Linear handoff и downstream execution проверяются владельцем `linear-agent-tools`, а не prose или scripts этого repository.
