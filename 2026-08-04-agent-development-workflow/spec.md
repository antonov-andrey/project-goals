# Локальный Linear Workflow Для Разработки

## Требуемый Результат

Локальная разработка через Codex CLI или Codex App должна образовать самостоятельный замкнутый workflow без Symphony. `agent-workflows:goal-brainstorm` подготавливает свободно пересматриваемые `goal.md` и `spec.md` в `project-goals`. Отдельный Linear workflow преобразует любой согласованный source в один полностью определённый Linear task graph. После успешной отправки Linear становится единственным владельцем operational state, зависимостей, попыток, review, acceptance, branches и pull requests.

Пользователь должен иметь возможность после завершения этой задачи подготовить следующую specification, отправить её в Linear, вручную запустить каждую готовую issue в новой Codex session, выполнить code, tests, commit, push, pull request, review, rework, merge и final acceptance, а затем очистить локальный workspace. Ни один этап этого цикла не должен требовать Symphony, AWS или историю brainstorm-чата.

## Проверенное Исходное Состояние

- Repository `agent-plugins` содержит plugins `agent-workflows`, `marketplace-agent-tools` и `workflow-container-agent-tools`; отдельного Linear plugin нет.
- Текущий `agent-workflows:goal-brainstorm` подготавливает implementation worktrees, создаёт `checkpoint.yaml`, активирует persistent goal и запрещает изменение active `goal.md` и `spec.md`.
- `project-goals/DESIGN.md` связывает каждую goal с одним common-prefix branch set, checkpoint publication, exclusive merge и explicit cleanup.
- Codex CLI `0.146.0` не имеет настроенного Linear MCP server.
- Remote repository `antonov-andrey/development-infrastructure` был пуст. После явного одобрения пользователя создан canonical `main` с одним нейтральным initial commit `18c8df7846dbd545f6b8370b590b7a9275d33951`; repository не содержит Product или infrastructure implementation.
- Symphony runtime, Symphony EC2 и Codex worker EC2 не развёрнуты и не входят в текущую implementation boundary.

## Владельцы

- `project-goals` владеет только результатом `goal-brainstorm`: tracked source contracts `goal.md` и `spec.md` до Linear handoff и их неизменяемой Git history после handoff.
- Plugin `agent-workflows` владеет generic source clarification и authoring workflow `goal-brainstorm`, а также generic Git publication workflow `git-commit`.
- Новый independently installable plugin `linear-agent-tools` владеет source-independent Linear task graph contract, Linear operations, ручными task procedures и локальным task-workspace lifecycle.
- Linear Project, Linear issues, issue relations, statuses, labels, comments и attachments являются единственным operational owner после успешного handoff.
- Git repositories владеют branches и commits. GitHub владеет pull requests, checks, reviews и merge result. Linear хранит связи с этими объектами, но не копирует их содержимое.
- Repository `development-infrastructure` владеет стабильным design будущей developer infrastructure, включая Symphony control plane и Codex worker pool. Он не владеет reusable Linear workflow skills.
- User-level Codex configuration владеет локальным Linear MCP connection и authentication. Credentials не принадлежат project artifacts, issue descriptions, logs или Git history.

## Scope

### `agent-plugins`

- Упростить `agent-workflows:goal-brainstorm` до подготовки, публикации и свободного пересмотра `project-goals/<common-prefix>/goal.md` и `spec.md` до handoff.
- Удалить из целевого `goal-brainstorm` implementation-worktree preparation, persistent goal activation, `checkpoint.yaml`, active-spec immutability и post-dispatch lifecycle.
- Создать plugin `linear-agent-tools` и добавить его в marketplace.
- Добавить skills:
  - `linear-agent-tools:task-graph-create`;
  - `linear-agent-tools:task-implement`;
  - `linear-agent-tools:task-review`;
  - `linear-agent-tools:task-accept`;
  - `linear-agent-tools:task-merge`;
  - `linear-agent-tools:task-cleanup`.
- Удалить из active provider старые `goal-checkpoint`, `goal-merge`, `goal-delete` и `goal-review` после завершения переходного lifecycle текущей goal. Не оставлять compatibility skills, forwarding references или runtime branches старой модели.
- Сохранить `agent-workflows:git-commit` как generic Git publication owner и использовать его из Linear task procedures по явной зависимости.
- Обновить plugin manifests, marketplace metadata, root design, README, tests и behavior-evaluation corpus в соответствии с новым owner set.

### `project-goals`

- Обновить stable design через serialized direct-main transaction `agent-workflows:goal-brainstorm`.
- Для новых goals после перехода определить directory shape только из `goal.md` и `spec.md`.
- Не создавать `task-graph.yaml`, `checkpoint.yaml`, task branch, linked worktree, bootstrap manifest, project-local copy или task-artifact link.
- Сохранить существующие исторические task directories и их `checkpoint.yaml` без переписывания history. Новый workflow не читает их как active compatibility state.
- Описать Linear handoff как конец authoring ownership этого repository, а не как отдельный tracked lifecycle state.

### `development-infrastructure`

- Создать canonical project foundation: `AGENTS.md`, `DESIGN.md`, `README.md`, `.gitignore` и `worktree-bootstrap.yaml` без infrastructure deployment code.
- Зафиксировать целевую архитектуру stable developer infrastructure в AWS account `227373271916`.
- Отделить current local Linear workflow от будущего Symphony runtime и AWS implementation.
- Не создавать CloudFormation stacks, IAM roles, VPC resources, EC2 instances, SSM resources или Codex worker lifecycle code в этой goal.

### User-Level Integration

- Подключить официальный Linear MCP server к Codex user configuration и выполнить interactive authentication.
- Не добавлять project-local Codex configuration ради Linear.
- Если официальный MCP не предоставляет одну обязательную graph operation, сначала доказать это contract probe. Затем реализовать минимальный Linear-owned GraphQL boundary в `linear-agent-tools`, не создавая generic CRUD wrapper и не передавая raw token coding-agent child process.

## Non-Goals

- Symphony service, poller, scheduler, retry queue и remote worker orchestration.
- Symphony EC2, Codex worker EC2 pool и AWS deployment.
- Автоматическое создание или удаление worker EC2.
- Production infrastructure, EKS или изменения `workflow-control-center`.
- Копирование Linear task graph в Git, `project-goals` или private execution journal.
- Поддержка старого и нового goal execution protocol одновременно после transition cleanup.

## Project-Goals Authoring И Handoff

`goal-brainstorm` создаёт один coherent source outcome. Source может охватывать несколько repositories и несколько independently executable Linear tasks. Scope gate проверяет coherence конечного результата и полную decomposability, а не требует одной implementation boundary для всей specification.

До успешного Linear handoff пользователь может пересматривать `goal.md` и `spec.md` обычным вызовом `goal-brainstorm`. `seal`, `active`, persistent goal и immutable-spec state в целевом authoring workflow отсутствуют.

`task-graph-create` получает exact canonical source identity:

- `source_kind`;
- canonical source URL или repository/path;
- exact immutable source revision, когда source versioned;
- human-readable outcome;
- complete source content.

Для `project-goals` source revision является full Git commit, а URL указывает на exact commit и root-relative `goal.md` и `spec.md`. Изменение later `main` не изменяет уже отправленный source snapshot.

Успешный handoff происходит только после полной проверки созданного Linear graph. С этого момента текущая работа меняется только через Linear issues, relations, comments и statuses. Изменение требований создаёт или изменяет Linear nodes. Новый независимый architecture brainstorm создаёт новый `project-goals` source; он не переписывает уже отправленный source snapshot.

## Linear Project И Task Graph

Один source graph создаёт один Linear Project. Project хранит exact source provenance и import status. Issues и blocker relations являются canonical task graph. Source provenance не предполагает `project-goals`; ручные, incident, audit и monitoring sources используют тот же contract.

Каждая issue обязана содержать:

- один concrete outcome;
- scope и non-goals;
- task role;
- canonical repository identities и base branches для code-mutating scope;
- required source contracts и standards;
- observable verification и acceptance;
- explicit blocker relations;
- branch и pull-request links, когда они существуют;
- source provenance;
- сведения, достаточные для запуска в fresh Codex session без brainstorm-chat history.

Task roles задаются прозрачными Linear labels:

- `task:implementation`;
- `task:review`;
- `task:acceptance`.

Отдельный required dispatch label разрешает Symphony-compatible execution. Exact label identity принадлежит Linear workflow configuration и одинаково применяется в local и future Symphony modes.

Review tasks зависят от проверяемых implementation tasks. Acceptance tasks зависят от review tasks и остальных необходимых graph nodes. Finding создаёт remediation issue или возвращает exact owning issue в `Rework`, добавляет blocker relation и предотвращает acceptance до повторной проверки. Review и acceptance не исправляют Product code скрыто в своём workspace.

## Graph Publication, Idempotency И Recovery

Linear API не считается multi-object atomic. `task-graph-create` использует activation barrier:

1. Создаёт или принимает exact Linear Project в non-dispatchable import state.
2. Записывает complete intended decomposition в Linear Project и назначает каждому node стабильный source key.
3. Создаёт или принимает issues в `Backlog` без dispatch label.
4. Создаёт или принимает exact blocker relations и metadata.
5. Перечитывает Project, все issues и relations и сравнивает их с intended graph.
6. Только после полного совпадения добавляет dispatch label и переводит root nodes без unresolved blockers в `Todo`.

Повторный вызов принимает только объекты с exact Project identity и stable source keys, создаёт отсутствующие объекты и исправляет только provider-owned import fields. Unknown conflicting objects не перезаписываются. Partial import остаётся полностью в Linear и не запускается Symphony. Отдельный `task-graph.yaml` или persistent private graph запрещён.

Source считается отправленным только после успешного activation barrier. До этого пользователь может явно отменить import, удалить или cancel созданные draft objects и продолжить пересмотр source.

## Workflow Status Contract

Linear team использует statuses:

- `Backlog`: идея, incomplete task или non-dispatchable import node; agent не запускается.
- `Todo`: task definition завершена; unresolved blockers остаются отдельной причиной `dispatchable=false`.
- `In Progress`: agent владеет текущей попыткой.
- `Human Review`: agent остановлен, workspace сохранён, result и evidence доступны человеку.
- `Rework`: human rejection или accepted finding требует новой попытки.
- `Merging`: человек одобрил exact candidate; agent может только довести его до merge.
- `Done`: terminal successful state.
- `Canceled`: terminal canceled state.

Symphony-compatible active states: `Todo`, `In Progress`, `Rework`, `Merging`. Terminal states: `Done`, `Canceled`. `Backlog` и `Human Review` являются inactive non-terminal states.

`dispatchable=true` требует одновременно:

- active state;
- required dispatch label;
- разрешённого assignee или delegate;
- отсутствия unresolved blockers;
- известного task role;
- complete issue contract.

Status transitions:

- `Backlog -> Todo`: task definition готова; blockers могут оставаться.
- `Todo -> In Progress`: manual runner или future Symphony доказал dispatchability и создал или принял exact workspace.
- `In Progress -> Human Review`: required result, verification, commit, push, PR и evidence готовы в применимой части.
- `Human Review -> Rework`: человек отклонил candidate или потребовал изменения.
- `Rework -> In Progress`: fresh Codex App Server thread принимает тот же issue, workspace, branches и PR без автоматического reset пользовательской работы.
- `Human Review -> Merging`: человек одобрил code-mutating candidate.
- `Merging -> Done`: exact approved PR merged, required checks зелёные, issue links и final commit подтверждены.
- `Human Review -> Done`: человек одобрил task, который не владеет mergeable PR.
- Любое non-terminal state может перейти в `Canceled` по явному решению человека.

Transient merge или provider failure может повторяться в `Merging`. Любое изменение source, test contract или generated artifact после human approval переводит issue в `Rework`; после новой проверки candidate снова проходит `Human Review`. Branch protection и required GitHub checks не обходятся.

## Local Task Workspace И Branch Contract

Каждая code-mutating Linear issue владеет одной deterministic task branch в каждом participating repository. Branch name содержит Linear issue identifier и normalized slug. Один cross-repository issue использует один и тот же branch identity во всех repositories. Exact branch и repository list записываются в issue и подтверждаются через GitHub attachments.

Local runner получает canonical repository URLs из issue и explicit user-level workspace root. Reusable plugin не содержит `/home/andrey/Projects` или другой user-specific absolute path. Для каждого repository он:

- находит canonical main checkout только по explicit workspace root и repository identity;
- проверяет origin и exact base commit;
- создаёт или принимает `.worktree/<linear-issue-identifier>`;
- создаёт или принимает exact task branch;
- сохраняет transaction state только в Git administration space;
- не меняет main checkout;
- не принимает совпадающие bytes как ownership proof;
- сохраняет unrelated user work.

Local transaction state содержит только crash-recovery сведения о Git mutations и не дублирует Linear task contract. После reconciliation или cleanup он удаляется.

Review и acceptance issues не получают фиктивную write branch. Они используют read-only candidate commits, merged main commits, PR refs или environment identities. Если issue реально получает code-remediation responsibility, она становится отдельной implementation issue с собственной branch.

Branch и PR title содержат Linear issue identifier, чтобы official GitHub integration связывал commits и pull requests с issue. Commit publication использует `agent-workflows:git-commit`. Каждый logical commit сразу push-ится.

## Manual Skills

### `linear-agent-tools:task-graph-create`

- Принимает любой explicit source и Linear destination.
- Выполняет semantic decomposition в bounded executable DAG.
- Показывает complete graph пользователю до external mutation.
- Публикует и проверяет graph через activation barrier.
- Не создаёт implementation workspace и не запускает tasks.

### `linear-agent-tools:task-implement`

- Работает только с `task:implementation` issue в `Todo`, `In Progress` или `Rework` и с `dispatchable=true` для нового запуска.
- Создаёт или принимает exact local worktrees и branches.
- Каждая `Todo` или `Rework` попытка запускается в fresh Codex thread.
- Реализует только issue scope, выполняет targeted и applicable full verification, создаёт logical commits, push и PR.
- Записывает concise evidence и links в Linear и переводит issue в `Human Review`.

### `linear-agent-tools:task-review`

- Работает только с `task:review` issue после закрытия blockers.
- Использует fresh thread и перечитывает current source contracts, exact candidate state и external state.
- Не исправляет findings скрыто.
- При finding создаёт или связывает remediation issue, делает её blocker и оставляет review незавершённым.
- При zero findings публикует concise result и переводит issue в `Human Review`.

### `linear-agent-tools:task-accept`

- Работает только с `task:acceptance` issue после закрытия blockers.
- Проверяет полный current outcome, а не только checklist, changed files или prior reports.
- При finding создаёт remediation blocker и требует нового полного acceptance pass после fix.
- При fresh zero-finding pass публикует concise result и переводит issue в `Human Review`.

### `linear-agent-tools:task-merge`

- Работает только с issue в `Merging` после exact human approval.
- Проверяет approved PR, base, head, required checks и branch protection.
- Повторяет transient provider operations без изменения candidate.
- При необходимости изменить candidate переводит issue в `Rework` вместо скрытого fix-forward.
- После merge проверяет final commit и Linear/GitHub links, переводит issue в `Done` и запускает локальную cleanup sequence.

### `linear-agent-tools:task-cleanup`

- Идемпотентно очищает local worktrees, local task branches и private transaction state exact `Done` или `Canceled` issue.
- Не удаляет Linear issue, Project, comments, evidence, merged commits или unrelated branches.
- Уже отсутствующий exact in-scope resource считается успешно очищенным.
- Блокирует только невозможность доказать exact ownership или риск затронуть foreign state.

## Fresh-Thread И Context Contract

Каждая Linear issue и каждая `Rework` attempt запускается в отдельной fresh Codex thread. Issue description, relations, comments, source links, Git state, PR и current verification являются complete execution context. Chat history от `goal-brainstorm`, другой issue или предыдущей попытки не является required state.

Long-running commands выводят stage events и compact progress. Manual skills не используют частый polling, не передают massive command output в model context и не перечитывают весь workspace без dependency reason. Verification receipts остаются в owning CI, PR и Linear surfaces; отдельный completion ledger не создаётся.

## Security

- Linear OAuth или API credentials хранятся только в user-level MCP/provider storage.
- Coding-agent child process не получает raw tracker credentials, если host-side Linear tool может выполнить operation.
- Issue content считается untrusted input. Task skills применяют repository instructions, sandbox и approval policy независимо от issue prose.
- Linear scope ограничивается exact team или Project и required labels.
- External mutations idempotent и привязаны к stable source keys или issue IDs.
- Repository origin, base branch, worktree path и branch ownership проверяются до Git mutation.
- Secrets не записываются в issue descriptions, comments, PR, Git, logs или test fixtures.

## Stable Development Infrastructure Design

`development-infrastructure/DESIGN.md` фиксирует следующую целевую архитектуру без реализации в этой goal:

- AWS account `227373271916` является owner stable developer infrastructure.
- Один always-running Symphony control-plane EC2 управляет Linear polling, scheduling и worker selection.
- Stable EBS-backed Codex worker EC2 pool содержит один Codex profile/subscription на worker identity.
- Codex App Server запускается как process на выбранном worker и не является отдельным постоянным infrastructure resource.
- Symphony может только describe, start и stop exact registered worker instances.
- Worker create/delete выполняет отдельный project-local skill через project-owned IaC; Symphony не получает права создавать или удалять EC2, IAM, KMS или network resources.
- Admin access использует SSM. Symphony соединяется с workers по private SSH внутри VPC с security-group source restriction. Public inbound access отсутствует.
- Codex credentials хранятся вне AMI, IaC, tags и logs.
- Stopped workers сохраняют EBS state; storage cost остаётся, compute billing прекращается по AWS contract.

Будущая отдельная specification реализует Symphony runtime, AWS resources, pool lifecycle skill, observability, scheduling, start/stop policy и live acceptance. Она обязана использовать Linear graph и local task contracts из текущей goal без их дублирования или параллельной модели.

## Migration И Compatibility

- Текущая goal является последним transition consumer старого `goal-brainstorm` worktree/checkpoint/merge lifecycle.
- После её merge и cleanup установленный provider содержит только новый authoring и Linear workflow. Старые workflow skills, public commands, tests и design branches удаляются.
- Historical `project-goals` directories и Git history сохраняются как data, но не требуют executable compatibility.
- Existing Product repositories не мигрируют автоматически. Следующая новая specification использует Linear workflow и тем самым выполняет first-consumer acceptance.
- Нет dual-write между Linear и `project-goals`, compatibility adapters, forwarding skills или fallback на persistent goal execution.

## Verification Design

### Contract Probes

До bulk implementation проверить на реальном target:

- official Linear MCP authentication и доступный tool surface;
- создание Project, issues, labels, statuses и blocker relations;
- чтение exact graph и idempotent lookup по stable source keys;
- GitHub issue-ID branch and PR linking;
- остановку active attempt при `Human Review` и terminal semantics `Done`/`Canceled` на уровне local manual runner contract.

Если MCP не покрывает одну operation, probe обязан назвать exact missing operation и подтвердить minimal GraphQL replacement boundary до его реализации.

### Automated Verification

- Plugin и skill structural validation для всех changed plugins и skills.
- `agent-plugins` complete pytest suite и focused behavior tests нового graph, state, Git workspace, recovery и cleanup owners.
- Temporary real Git repositories проверяют branch/worktree creation, cross-repository identity, collision rejection, interrupted transactions, user-work preservation, merge handoff и idempotent cleanup.
- Linear transport tests используют typed fixtures только для deterministic boundary behavior; они не заменяют real integration profile.
- Behavior-evaluation corpus покрывает direct, indirect, negative и overlap triggers нового `goal-brainstorm` и всех Linear skills на target model.
- Semantic owner audit подтверждает отсутствие старого active lifecycle, duplicated task graph, compatibility wrappers и stale provider metadata.

### Real Local Acceptance Без Symphony

В isolated real Linear Project выполнить:

1. Создание source-independent graph минимум из implementation, review и acceptance nodes.
2. Проверку unresolved blocker как `dispatchable=false` без отдельного blocked status.
3. Прерывание graph import до activation barrier и idempotent reconciliation без duplicate issues.
4. Переход root issue `Todo -> In Progress -> Human Review` через fresh Codex session.
5. Переход `Human Review -> Rework -> In Progress` в новой fresh session с сохранением branch и workspace.
6. Проверку branch и disposable PR linking по Linear identifier.
7. Проверку `Human Review -> Merging`, merge preconditions и возврата в `Rework` при candidate mutation.
8. Запуск review после implementation blockers и acceptance после review blockers.
9. Создание remediation blocker из finding и повторный fresh review или acceptance pass после fix.
10. Terminal transition и idempotent local cleanup для `Done` и `Canceled`.

Acceptance обязана доказать, что новый clean Codex thread может выбрать одну ready issue, восстановить весь необходимый context только из Linear, Git/GitHub и stable source contracts и довести task до её следующего human или terminal boundary. Наличие Symphony, AWS resource или brainstorm chat history запрещено как prerequisite.

### Terminal Completion Audit

После предполагаемого завершения заново проверить полный current scope против `goal.md`, этой specification, всех changed stable owners, installed local plugins, Linear configuration и real integration state. Любая incomplete, contradictory, stale или unverified requirement исправляется, affected verification повторяется, затем полный audit начинается заново. Goal завершается только после нового полного прохода без findings.
