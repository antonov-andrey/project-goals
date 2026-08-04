# Локальный Linear Workflow Для Разработки

## Требуемый Результат

Локальная разработка через Codex CLI или Codex App должна образовать самостоятельный замкнутый workflow без Symphony. `agent-workflows:goal-brainstorm` подготавливает свободно пересматриваемые `goal.md` и `spec.md` в `project-goals`. Отдельный Linear workflow преобразует любой согласованный source в один полностью определённый Linear task graph. После успешной отправки Linear становится единственным владельцем operational lifecycle, зависимостей, попыток, review, acceptance и связей с branches и pull requests; сами Git objects и PR остаются у Git и GitHub.

Пользователь должен иметь возможность после завершения этой задачи подготовить следующую specification, отправить её в Linear, вручную запустить каждую готовую issue в новой Codex session, выполнить code, tests, commit, push, pull request, review, rework, merge и final acceptance, а затем очистить локальный workspace. Ни один этап этого цикла не должен требовать Symphony, AWS или историю brainstorm-чата.

## Проверенное Исходное Состояние

- Repository `agent-plugins` содержит plugins `agent-workflows`, `marketplace-agent-tools` и `workflow-container-agent-tools`; отдельного Linear plugin нет.
- Текущий `agent-workflows:goal-brainstorm` подготавливает implementation worktrees, создаёт `checkpoint.yaml`, активирует persistent goal и запрещает изменение active `goal.md` и `spec.md`.
- `project-goals/DESIGN.md` связывает каждую goal с одним common-prefix branch set, checkpoint publication, exclusive merge и explicit cleanup.
- В Codex CLI `0.146.0` настроен и через OAuth авторизован user-level MCP server `linear` с exact URL `https://mcp.linear.app/mcp` и scopes `read`/`write`; project-local Linear configuration отсутствует.
- Авторизованный Linear user имеет ID `71a7a6e4-9a30-4050-9106-de34a9e25b6f`, display name `antonov.andrey`, административные права, active membership и не является guest.
- OAuth workspace содержит один active team: ID `353a3554-d4c0-4838-bf96-6f8798448419`, name `Andrey Antonov`, key `AND`; Linear Projects ещё не созданы.
- В team присутствуют стандартные issue statuses `Backlog`, `Todo`, `In Progress`, `Done`, `Canceled` и system-managed `Duplicate`; требуемые `Human Review`, `Rework` и `Merging` отсутствуют.
- Текущий official MCP tool surface читает issue statuses и выполняет основные Project/issue/document/label operations, но не предоставляет mutation для team issue statuses или workspace Project statuses. Это доказанный configuration gap для contract probe; minimal typed GraphQL boundary допускается только для этих отсутствующих operations и других отдельно доказанных gaps.
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
- Initial `write` и каждая revision атомарно публикуют complete pair `goal.md`/`spec.md`; spec-only current directory и unpublished approved revision запрещены.
- Удалить из целевого `goal-brainstorm` implementation-worktree preparation, persistent goal activation, `checkpoint.yaml`, active-spec immutability и post-dispatch lifecycle.
- Создать plugin `linear-agent-tools` и добавить его в marketplace.
- Добавить skills:
  - `linear-agent-tools:workflow-configure`;
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
- `linear-agent-tools:workflow-configure` обязан создать или проверить exact team issue workflow и workspace-level Project workflow до первой graph publication; `task-graph-create` не исправляет global configuration скрыто.
- Если официальный MCP не предоставляет одну обязательную graph operation, сначала доказать это contract probe. Затем реализовать минимальный Linear-owned GraphQL boundary в `linear-agent-tools`, не создавая generic CRUD wrapper и не передавая raw token coding-agent child process.

### Linear Acceptance Account Binding

Real integration и end-to-end acceptance этой task выполняются только через user-level MCP profile `linear`, его текущий OAuth workspace и exact team `353a3554-d4c0-4838-bf96-6f8798448419` (`Andrey Antonov`, `AND`) от имени exact Linear user `71a7a6e4-9a30-4050-9106-de34a9e25b6f`. OAuth workspace identity является частью provider authentication context; team name или email не используются как substitute exact identity.

Эта binding принадлежит только acceptance текущей task и не становится default, hardcoded allowlist или Product configuration внутри reusable plugin. Перед первой Linear mutation и после reconnect skill перечитывает authenticated user и team, проверяет exact IDs, `isAdmin=true`, `isGuest=false` и active membership. Несовпадение останавливает operation до mutation и требует явного выбора другого destination; автоматический поиск похожего workspace, user или team по имени запрещён.

В рамках явно запрошенного пользователем real testing эта task вправе в exact bound destination создать или принять только утверждённые missing statuses `Human Review`, `Rework`, `Merging`, role labels, label `agent:codex`, требуемые Project statuses и один isolated acceptance Project с его issues, relations, document и comments. Existing standard statuses принимаются по semantic category и не переименовываются, не удаляются и не создаются повторно. Foreign labels, statuses, Projects и другие workspace settings не изменяются. Planned global delta всё равно показывается в execution output, но отдельное повторное разрешение на тот же exact closed mutation set не требуется.

Acceptance Project получает user-visible name с префиксом `Acceptance` и provider-owned identity из exact team ID и source fingerprint, поэтому repeated acceptance reconciles тот же Project вместо создания duplicates. Project и issues после успешного теста сохраняются в Linear как `Completed` history; отменённый проход сохраняется как `Canceled`. Cleanup удаляет только declared Git, local и external runtime resources и никогда не удаляет Linear history.

GitHub integration проверяется отдельно до создания code-mutating acceptance node. Если установка или расширение repository access требует browser/admin action, workflow останавливается до Linear graph activation, показывает один exact bounded step и после его выполнения повторяет read-back. Пустой diff list или вручную добавленная PR URL не считаются доказательством integration.

OAuth tokens, API keys и GraphQL credentials не записываются в `project-goals`, plugin source, Linear issues, Git branches, logs или verification receipts. Stable task contract хранит только MCP profile name и non-secret Linear object IDs.

## Provider Implementation Structure

Старый mixed `plugins/agent-workflows/lib/goal_lifecycle/` не сохраняется под новым именем. Из него переносится только реально reusable serialized direct-main authoring transaction в cohesive `goal_authoring` package; checkpoint, merge, deletion, persistent-goal, participant-worktree и sealed-state owners удаляются вместе с tests и public surfaces.

Новый provider использует owner packages с односторонними зависимостями:

```text
plugins/
  agent-workflows/
    lib/goal_authoring/
      model.py
      repository.py
      transaction.py
      workflow.py
    skills/goal-brainstorm/
      SKILL.md
      references/specification-contract.md
      scripts/source.py
  linear-agent-tools/
    .codex-plugin/plugin.json
    lib/
      linear_boundary/
        model.py
        transport.py
        [graphql.py]
      git_host/
        model.py
        pull_request.py
      task_graph/
        issue-contract.md
        model.py
        publication.py
        reconciliation.py
      task_workspace/
        model.py
        lock.py
        repository.py
        bootstrap.py
        transaction.py
      task_cleanup/
        model.py
        resource.py
        reconciliation.py
      verification/
        model.py
        receipt.py
        invalidation.py
    skills/
      workflow-configure/
      task-graph-create/
      task-implement/
      task-review/
      task-accept/
      task-merge/
      task-cleanup/
```

`task_graph/issue-contract.md` владеет одним shared rendered issue template с role-conditional sections: Outcome, Source, Scope, Non-goals, Delivery kind, Repositories and base branches, Required contracts and skills, Blockers, Resource ownership/lifetime, Verification plan, Human decision boundary и Evidence/links. Template не копируется по skills и после publication становится обычным visible Linear issue content.

Skill `SKILL.md` хранит orchestration и decision contract, thin `scripts/` только вызывает deterministic owners. Linear transport, graph reconciliation, Git workspace transactions, verification receipts и cleanup не смешиваются в одном manager или flat `lib/*`. Facade владеет только sequencing и explicit dependency wiring. Official MCP operations вызываются как host tools; один minimal typed GraphQL adapter появляется только для доказанных gaps и не дублируется между skills. GitHub/PR operations также проходят через один shared typed boundary, а не через разные ad hoc shell sequences.

Square brackets в layout обозначают conditional file: `graphql.py` существует только если real contract probe доказал gap official MCP. Empty placeholder packages, pass-through compatibility modules и speculative abstractions не создаются.

Stale `worktree.py`, worktree lifecycle reference и goal-lifecycle names не остаются в simplified authoring skill. Новый entrypoint выражает только idempotent source operations `write` и `validate`; `write` выполняет один serialized commit/push exact `goal.md`/`spec.md` delta и одинаково обслуживает initial authoring и pre-handoff revision. Hidden activation, seal или unpublished local state отсутствуют.

Root README и plugin-local reference содержат один короткий manual quickstart: configure destination, publish graph, открыть fresh thread для ready issue, выполнить role skill, принять `Human Review`, выполнить `Merging`, затем review, acceptance и final cleanup. Инструкции не требуют знания старого goal lifecycle.

## Non-Goals

- Symphony service, poller, scheduler, retry queue и remote worker orchestration.
- Symphony EC2, Codex worker EC2 pool и AWS deployment.
- Concurrent autonomous dispatch одной issue с нескольких hosts; current manual mode использует один configured local workspace и host-local lock, а distributed claim authority принадлежит следующей Symphony specification.
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

После handoff `task-graph-create` может применить user-approved Linear-native graph delta только к existing Project в `In Progress`, но не перечитывает later `project-goals/main` как новую версию исходного source. Delta provenance указывает на exact Linear decision, finding или manual source. Новые nodes сначала создаются в `Backlog` без `agent:codex`, затем получают все relations и проходят read-back; label добавляется пока node inactive, а `Todo` является её последней activation mutation. Delta сохраняет или расширяет downstream review, acceptance и final-cleanup blockers и не может обойти уже обязательную проверку. Existing unknown fields и unrelated nodes не перезаписываются. `Completed` или `Canceled` Project не reopen-ится этим workflow; новая independent work создаёт новый source graph.

## Linear Project И Task Graph

Один source graph создаёт один Linear Project. Project хранит exact source provenance и import status. Issues и blocker relations являются canonical task graph. Source provenance не предполагает `project-goals`; ручные, incident, audit и monitoring sources используют тот же contract.

Каждая issue обязана содержать:

- один concrete outcome;
- scope и non-goals;
- task role;
- exact intended assignee or delegate identity for agent-executable work;
- delivery kind `code`, `evidence`, `cleanup` или `human`; contract probe является `task:implementation` с `delivery_kind=evidence`, а review/acceptance используют `evidence`, не скрывая эти фазы внутри большой code issue;
- canonical repository identities и base branches для code-mutating scope;
- required source contracts и standards;
- observable verification plan, invalidation dependencies и acceptance;
- explicit blocker relations;
- exact local или external resource ownership и lifetime `attempt`, `issue` или `project`, когда task создаёт ресурсы;
- branch и pull-request links, когда они существуют;
- source provenance;
- compact task-relevant requirements and exact source sections, sufficient для запуска в fresh Codex session без brainstorm-chat history и без копирования полного source во все issues.

Task roles задаются прозрачными Linear labels:

- `task:implementation`;
- `task:review`;
- `task:acceptance`;
- `task:cleanup`;
- `task:human`.

Allowed role/delivery pairs: `implementation -> code|evidence`, `review -> evidence`, `acceptance -> evidence`, `cleanup -> cleanup`, `human -> human`. Другие комбинации не активируются.

Required dispatch label имеет exact identity `agent:codex` и разрешает Symphony-compatible execution. Agent-executable nodes получают его только через activation barrier. `task:human` его не получает и никогда не dispatch-ится агенту.

Decomposition предпочитает один independently verifiable owner slice и один repository/PR на issue. Cross-repository issue допустима только когда результат нельзя честно принять по отдельности; тогда issue перечисляет все repositories, ordered merge plan и partial-merge recovery. Неопределённое внешнее свойство сначала получает blocking evidence-only contract-probe issue. Bulk implementation не начинается до `Done` соответствующего probe.

Initial source graph не активируется с incomplete node: unresolved task-definition question возвращается к source authoring или оформляется полностью определённой `task:human`/evidence probe. `Backlog` внутри publication является staging state, а не способом спрятать незаданную работу в active Project.

Review tasks зависят от проверяемых implementation tasks. Отдельный review node выполняет post-merge semantic review; pre-merge human decision принадлежит состоянию `Human Review` самой implementation issue. Acceptance tasks зависят от review tasks и остальных необходимых graph nodes. Finding для незавершённой issue может вернуть её в `Rework`. Finding против merged `Done` state всегда создаёт новую remediation implementation issue с новой branch. Remediation становится blocker текущей review или acceptance issue, а эта issue возвращается в `Todo`; после закрытия blocker она запускает новый полный pass в fresh thread. Review и acceptance не исправляют Product code скрыто в своём workspace.

Каждый graph содержит последнюю `task:cleanup` issue, заблокированную final acceptance. Она удаляет declared project-lifetime resources, reconciles issue-owned local state и доказывает отсутствие остаточного owned state; когда удалять нечего, этот proof всё равно закрывает Project единообразно. Project завершается только после terminal acceptance, cleanup `Done` и отсутствия незакрытых remediation blockers. Linear issue сама является canonical task goal; отдельный per-issue `project-goals` artifact, `task-graph.yaml` или persistent harness goal не требуется.

Resource lifetime имеет точную семантику: `attempt` очищается при завершении или перед recovery следующей attempt; `issue` сохраняется до terminal issue и последнего declared consumer; `project` сохраняется до final cleanup node. Cleanup идемпотентен и выполняется project-owned command. Resource без exact owner identity или cleanup contract не создаётся.

## Linear Workflow Configuration

`linear-agent-tools:workflow-configure` принимает exact workspace и team, показывает planned team-global mutations и выполняет их только после явного подтверждения. Он создаёт или принимает exact issue statuses в Linear fixed categories:

- `Backlog` — `Backlog`;
- `Todo` — `Unstarted`;
- `In Progress`, `Human Review`, `Rework`, `Merging` — `Started`;
- `Done` — `Completed`;
- `Canceled` — `Canceled`.

Он также создаёт или принимает role labels, required label `agent:codex`, exact Project statuses `Planned`, `In Progress`, `Completed`, `Canceled` в соответствующих fixed Project categories и проверяет GitHub integration prerequisite. Совпадающее имя в неправильной category, неоднозначный duplicate или foreign conflicting configuration не переписывается. Skill публикует concise verification result; secrets и OAuth material остаются в user-level provider storage.

Если GitHub integration требует browser/admin action, skill не имитирует success: он выдаёт exact bounded human step, не начинает graph publication и после выполнения повторяет read-back probe. Closed acceptance требует реального issue-ID link, поэтому отсутствие integration не заменяется вручную вставленной URL.

## Graph Publication, Idempotency И Recovery

Linear API не считается multi-object atomic. `task-graph-create` использует activation barrier:

`source_fingerprint` является SHA-256 canonical source identity и content. Каждый approved node получает user-visible stable `node_key`, не зависящий от изменяемого title или Linear issue number; full source key равен паре `(source_fingerprint, node_key)`. Project и issue descriptions хранят эти keys для exact lookup и conflict detection.

1. Создаёт или принимает exact Linear Project в status `Planned`, который всегда означает `dispatchable=false` для всех его issues.
2. Записывает visible provider-owned import plan в Linear Project document: canonical source identity, content fingerprint, approved normalized node definitions, stable source keys и expected edges. Это Linear-local transaction envelope и import receipt, а не второй operational graph.
3. Создаёт или принимает issues в `Backlog` без dispatch label.
4. Создаёт или принимает exact blocker relations и metadata.
5. Пока Project остаётся `Planned`, добавляет `agent:codex` только agent-executable nodes и переводит все полностью определённые nodes в `Todo`; unresolved blockers независимо сохраняют `dispatchable=false`.
6. Перечитывает Project, import plan, все issues, relations, labels и statuses и сравнивает их с approved graph.
7. Только после полного совпадения одним Project transition меняет `Planned -> In Progress`. Это единственная activation mutation.

Повторный вызов принимает только объекты с exact Project identity и stable source keys, создаёт отсутствующие объекты и исправляет только provider-owned import fields. Unknown conflicting objects не перезаписываются. Partial import остаётся полностью в Linear и не запускается Symphony. Отдельный `task-graph.yaml` или persistent private graph запрещён.

Source считается отправленным только после доказанного Project transition в `In Progress`. До этого пользователь может явно отменить import, перевести exact draft Project и его draft issues в `Canceled` и продолжить пересмотр source. После `In Progress` import plan не переигрывает graph из source: дальнейшие изменения принадлежат Linear.

Явная отмена уже active Project переводит незавершённые issues в `Canceled`, останавливает новые dispatch и сохраняет Linear history. Exact terminal cleanup остаётся разрешённой ручной операцией даже у canceled Project; после cleanup Project остаётся `Canceled`, а не `Completed`. Частично отсутствующие owned resources являются success и не препятствуют завершению cleanup.

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

Symphony-compatible active states: `Todo`, `In Progress`, `Rework`, `Merging`. Terminal states: `Done`, `Canceled`. `Backlog` и `Human Review` являются inactive non-terminal states. Linear category `Started` у `Human Review` не делает его dispatchable: scheduler использует exact active-state allowlist.

`dispatchable=true` требует одновременно:

- active state;
- status category `started` owning Linear Project, represented by exact status `In Progress`;
- required dispatch label;
- assignee или delegate, совпадающего с exact execution identity issue;
- отсутствия unresolved blockers;
- известного task role;
- complete issue contract.

Status transitions:

- `Backlog -> Todo`: task definition готова; blockers могут оставаться.
- `Todo -> In Progress`: manual runner или future Symphony доказал dispatchability, зафиксировал attempt baseline и создал или принял exact workspace.
- `In Progress -> Human Review`: required result, verification, commit, push, PR, required CI и evidence готовы в применимой части.
- `Human Review -> Rework`: человек отклонил candidate или потребовал изменения.
- `Rework -> In Progress`: fresh Codex thread принимает тот же issue, workspace, branches и PR без автоматического reset пользовательской работы.
- `Human Review -> Merging`: человек одобрил code-mutating candidate.
- `Merging -> Done`: exact approved PR merged, required checks зелёные, issue links и final commit подтверждены.
- `Human Review -> Done`: человек одобрил task, который не владеет mergeable PR.
- `Todo -> Done`: только `task:human`, когда назначенный человек выполнил explicit outcome и приложил required evidence.
- `In Progress -> Done`: только `task:cleanup` после успешного exact cleanup/reconciliation и Project completion proof; запуск этой issue после terminal acceptance является явной cleanup operation.
- Любое non-terminal state может перейти в `Canceled` по явному решению человека.

Transient merge или provider failure может повторяться в `Merging`. Любое изменение source, test contract или generated artifact после human approval переводит issue в `Rework`; после новой проверки candidate снова проходит `Human Review`. Branch protection и required GitHub checks не обходятся.

## Local Task Workspace И Branch Contract

Каждая code-mutating Linear issue владеет одной deterministic task branch `linear/<lowercase-issue-identifier>` в каждом participating repository. Один cross-repository issue использует один и тот же branch identity во всех repositories. Local worktree basename равен lowercase Linear issue identifier. Exact branch, repository list и base branches записываются в issue и подтверждаются через GitHub attachments.

Local runner получает canonical repository URLs из issue и explicit user-level `LINEAR_AGENT_WORKSPACE_ROOT`. Reusable plugin не содержит `/home/andrey/Projects` или другой user-specific absolute path. Перед task mutation runner получает один non-blocking host-local lock по Linear issue identity. Другая active local session с тем же lock предотвращает concurrent execution; process exit освобождает lock, а durable Git transaction state позволяет fresh session продолжить прерванную attempt. Для каждого repository runner:

- находит canonical main checkout только по explicit workspace root и repository identity;
- fetch-ит exact remote base branch и при первом dispatch атомарно записывает его current commit как attempt baseline в Linear evidence;
- создаёт или принимает `.worktree/<linear-issue-identifier>`;
- создаёт или принимает exact task branch;
- применяет repository-owned `worktree-bootstrap.yaml` через generic copy/link resource и optional direct-argv cleanup binding, не копируя project-specific bootstrap policy в plugin и не используя shell evaluation;
- сохраняет transaction state только в Git administration space;
- не меняет main checkout;
- не принимает совпадающие bytes как ownership proof;
- сохраняет unrelated user work.

Host-local lock и transaction state содержат только concurrency и crash-recovery сведения и не дублируют Linear task contract. После reconciliation или cleanup durable state удаляется.

`Rework` принимает существующую remote branch, pull request и workspace без destructive reset и не меняет baseline скрыто. Base update или rebase являются candidate mutation, записываются в Linear и инвалидируют прежнее human approval. При crash fresh session восстанавливает состояние из Linear, remote Git/GitHub и owned Git administration transaction, а не из previous chat.

Review и acceptance issues не получают фиктивную write branch. Они используют read-only candidate commits, merged main commits, PR refs или environment identities. Если issue реально получает code-remediation responsibility, она становится отдельной implementation issue с собственной branch.

Branch и PR title содержат Linear issue identifier, чтобы official GitHub integration связывал commits и pull requests с issue. Commit publication использует `agent-workflows:git-commit`. Каждый logical commit сразу push-ится.

## Verification Receipts И Execution Efficiency

Task skills строят dependency-aware verification plan из issue contract и repository instructions. Targeted checks выполняются после завершённого owner slice, applicable full suite — для frozen candidate, live acceptance — только для exact deployed candidate, а fresh terminal semantic audit — после последнего fix.

Reusable receipt требует exact key из command, repository commit set, recursive submodule commits, relevant dependency-lock fingerprints и external environment or release identity. Concise receipt с outcome, UTC time и link на owning log или CI хранится в Linear issue или GitHub check. Совпадение только command или working-tree bytes не разрешает reuse. Mutation инвалидирует только receipts, чьи declared inputs изменились; final full suite и terminal semantic audit не заменяются cached partial checks.

Переход в `Human Review` публикует immutable candidate fingerprint: exact PR head set для code delivery либо exact evidence/environment receipt set для non-code delivery. Human transition в `Merging` или `Done` относится только к этому fingerprint. Любая последующая mutation делает approval stale; `task-merge` или соответствующий task skill переводит issue в `Rework`, а не пытается угадать намерение человека.

Long-running operations сразу публикуют current stage и затем bounded compact progress. Runner ожидает provider event или разумный interval, не делает частый polling и не переносит raw large logs в Codex context. Raw artifacts остаются у CI, repository test output или owning observability system.

## Workflow Telemetry

Linear status history остаётся canonical source wall-clock lifecycle. После каждой agent attempt task skill публикует один concise structured summary: attempt identity, role, start/end UTC, outcome, changed commit set, verification receipt hit/miss counts, external wait duration и token usage только когда Codex surface предоставляет его без разбора private chat logs. GitHub checks владеют CI duration. Отдельная telemetry database или дублирующий execution journal в текущем local workflow не создаётся.

Real acceptance сохраняет baseline одной complete disposable flow, чтобы следующая Symphony specification могла сравнить queue, startup, execution, review и merge phases с local mode. Prompt bodies, secrets и raw command output в Linear telemetry не попадают.

## Manual Skills

### `linear-agent-tools:workflow-configure`

- Подключает или проверяет официальный Linear MCP в user-level Codex configuration и проводит interactive OAuth без сохранения credentials в project files.
- Создаёт или проверяет exact team statuses, workspace-level Project statuses, role labels и `agent:codex` после preview и explicit approval.
- Не создаёт Project task graph и не меняет Product repositories.

### `linear-agent-tools:task-graph-create`

- Принимает любой explicit source и новый либо existing Linear destination; existing active Project изменяется только через Linear-native approved delta contract.
- Выполняет semantic decomposition в bounded executable DAG.
- Показывает complete graph пользователю до external mutation.
- Публикует и проверяет graph через activation barrier.
- Не создаёт implementation workspace и не запускает tasks.

### `linear-agent-tools:task-implement`

- Работает только с `task:implementation` issue в `Todo`, `In Progress` или `Rework` и с `dispatchable=true` для нового запуска; evidence-only probe проходит тот же lifecycle без фиктивной branch или PR.
- Создаёт или принимает exact local worktrees и branches.
- Каждая `Todo` или `Rework` попытка запускается в fresh Codex thread.
- Реализует только issue scope, выполняет targeted и applicable full verification, создаёт logical commits, push и PR.
- Записывает reusable verification receipts, candidate fingerprint, concise evidence и links в Linear и переводит issue в `Human Review` только после applicable CI result.

### `linear-agent-tools:task-review`

- Работает только с `task:review` issue после закрытия blockers.
- Принимает `Todo`, `In Progress` или `Rework`; новая попытка переводит issue в `In Progress` и запускается в fresh thread.
- Использует fresh thread и перечитывает current source contracts, exact candidate state и external state.
- Не исправляет findings скрыто.
- При finding создаёт или связывает remediation issue, делает её blocker и возвращает review в `Todo`.
- При zero findings публикует concise result и переводит issue в `Human Review`.

### `linear-agent-tools:task-accept`

- Работает только с `task:acceptance` issue после закрытия blockers.
- Принимает `Todo`, `In Progress` или `Rework`; новая попытка переводит issue в `In Progress` и запускается в fresh thread.
- Проверяет полный current outcome, а не только checklist, changed files или prior reports.
- При finding создаёт remediation blocker, возвращает acceptance в `Todo` и требует нового полного acceptance pass после fix.
- При fresh zero-finding pass публикует concise result и переводит issue в `Human Review`.

### `linear-agent-tools:task-merge`

- Работает только с issue в `Merging` после exact human approval.
- Проверяет exact approved PR set, bases, heads, required checks, ordered cross-repository merge plan и branch protection.
- Повторяет transient provider operations без изменения candidate.
- При необходимости изменить candidate переводит issue в `Rework` вместо скрытого fix-forward.
- После каждого merge проверяет final commit и Linear/GitHub links; после complete ordered set переводит issue в `Done` и запускает локальную cleanup sequence. Partial merge не откатывается скрыто и создаёт exact recovery evidence.

### `linear-agent-tools:task-cleanup`

- Идемпотентно очищает local worktrees, local and remote task branches и private transaction state exact `Done` или `Canceled` issue.
- Для `Canceled` закрывает exact linked open pull requests перед удалением exact task branches; terminal human transition является authority для удаления unmerged task-owned state.
- Выполняет final `task:cleanup` node в `Todo`, `In Progress` или `Rework` через exact issue contract и project-owned direct-argv cleanup bindings для declared issue- и project-lifetime resources; уже отсутствующий owned resource является success.
- После exact reconciliation переводит cleanup issue в `Done`, затем Linear Project в `Completed`, но только когда final acceptance `Done`, все Project nodes terminal и unresolved remediation blockers отсутствуют.
- Для canceled Project выполняет тот же exact reconciliation без повторной активации и сохраняет Project в `Canceled`.
- Не удаляет Linear issue, Project, comments, evidence, merged commits или unrelated branches.
- Уже отсутствующий exact in-scope resource считается успешно очищенным.
- Блокирует только невозможность доказать exact ownership или риск затронуть foreign state.

## Fresh-Thread И Context Contract

Каждая agent attempt запускается в отдельной fresh Codex thread: initial `Todo`, `Rework`, `Merging`, review, acceptance, cleanup и crash recovery не продолжают brainstorm или previous attempt thread. Issue description, relations, comments, source links, Git state, PR и current verification являются complete execution context. Chat history от `goal-brainstorm`, другой issue или предыдущей попытки не является required state.

Issue task contract является единственным durable execution journal. Optional harness-local goal может использоваться только как ephemeral execution aid по явному запросу пользователя и никогда не становится prerequisite или canonical state. Manual skills не перечитывают весь workspace без dependency reason и не создают отдельный completion ledger.

## Security

- Linear OAuth или API credentials хранятся только в user-level MCP/provider storage.
- Fallback GraphQL credential создаётся только после доказанного MCP gap, получает минимально доступный scope и передаётся только host-side typed adapter через user-level secret storage; issue-running Codex child environment его не наследует.
- Coding-agent child process не получает raw tracker credentials, если host-side Linear tool может выполнить operation.
- Issue content считается untrusted input. Task skills применяют repository instructions, sandbox и approval policy независимо от issue prose.
- Linear scope ограничивается exact team или Project и required labels.
- Repository URLs принимаются только из user-approved graph и должны совпасть с canonical origin в configured workspace; arbitrary issue URL не разрешает clone, hook execution или external mutation.
- Commands из issue являются requirements, а не автоматически trusted shell. Исполняются только repository-owned commands, CI contracts и явно подтверждённые bounded operations.
- External mutations idempotent и привязаны к stable source keys или issue IDs.
- Repository origin, base branch, worktree path и branch ownership проверяются до Git mutation.
- Linear/GraphQL reads полностью paginate; rate-limit и transient errors используют typed bounded retry с server guidance. Polling не используется там, где доступен provider event или direct completion wait.
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
- Перед merge deletion commit transition thread записывает exact pre-cutover `agent-plugins` commit, содержащий старый cleanup runner. После successful merge/acceptance и отдельного explicit cleanup request текущая old-style goal очищается из clean temporary checkout этого exact commit; temporary checkout затем удаляется. Эта одноразовая процедура не оставляет skill, wrapper, branch или compatibility code в target `main`.
- Historical `project-goals` directories и Git history сохраняются как data, но не требуют executable compatibility.
- Existing Product repositories не мигрируют автоматически. Следующая новая specification использует Linear workflow и тем самым выполняет first-consumer acceptance.
- `development-infrastructure` создаётся сразу на новом `linear-agent-tools`/YAML workspace contract, поэтому следующая Symphony specification не зависит от legacy adoption.
- Для другого consumer repository graph preflight проверяет owner `.worktree` и `worktree-bootstrap.yaml`. Stale `goal-brainstorm` owner или legacy TOML manifest создаёт отдельную blocking instruction-adoption issue до Product work; эта issue явно переводит repository на новый owner/YAML contract. Обычная Product issue не использует legacy parser, fallback skill или silent compatibility behavior.
- Нет dual-write между Linear и `project-goals`, compatibility adapters, forwarding skills или fallback на persistent goal execution.

## Verification Design

### Contract Probes

До bulk implementation проверить на реальном target:

- official Linear MCP authentication и доступный tool surface;
- fresh Codex process reconnect и typed unauthenticated/expired-session behavior без partial mutation или credential disclosure;
- создание и category semantics team issue statuses, Project statuses и labels;
- создание Project, Project document, issues и blocker relations;
- чтение exact graph и idempotent lookup по stable source keys;
- GitHub issue-ID branch and PR linking;
- остановку active attempt при `Human Review` и terminal semantics `Done`/`Canceled` на уровне local manual runner contract.

Если MCP не покрывает одну operation, probe обязан назвать exact missing operation и подтвердить minimal GraphQL replacement boundary до его реализации.

### Automated Verification

- Plugin и skill structural validation для всех changed plugins и skills.
- Выполнить canonical plugin cachebuster/reinstall, открыть fresh Codex process и доказать discovery нового `linear-agent-tools`, simplified `goal-brainstorm` и отсутствие удалённых goal lifecycle skills.
- `agent-plugins` complete pytest suite и focused behavior tests нового configuration, graph, activation barrier, state, Git workspace, receipt invalidation, recovery и cleanup owners.
- Temporary real Git repositories проверяют branch/worktree creation, cross-repository identity, collision rejection, interrupted transactions, user-work preservation, merge handoff и idempotent cleanup.
- Legacy-consumer fixture проверяет, что stale worktree owner или TOML bootstrap создаёт blocking adoption node и не разрешает dispatch Product node; после migration тот же repository проходит canonical YAML path без fallback.
- Linear transport tests используют typed fixtures только для deterministic boundary behavior; они не заменяют real integration profile.
- Behavior-evaluation corpus покрывает direct, indirect, negative и overlap triggers нового `goal-brainstorm` и всех Linear skills на current target model `gpt-5.6-sol`.
- Semantic owner audit подтверждает отсутствие старого active lifecycle, duplicated task graph, compatibility wrappers и stale provider metadata.
- Transition test из exact pre-cutover commit очищает worktrees/refs текущей goal после target merge без зависимости от удалённых skills в installed target provider.

### Real Local Acceptance Без Symphony

В isolated real Linear Project выполнить:

1. Configuration dry-run, idempotent apply и read-back exact issue status categories, Project statuses и labels.
2. Создание source-independent graph минимум из evidence-only probe, code implementation, review, acceptance и cleanup nodes.
3. Проверку blocked issue в `Todo` с `dispatchable=false` без отдельного blocked status и без дополнительного status transition после закрытия blocker.
4. Прерывание graph import до Project activation и idempotent reconciliation без duplicate issues; ни одна issue Project в `Planned` не запускается.
5. Единственный activation transition `Planned -> In Progress` после полного read-back proof.
6. Переход root issue `Todo -> In Progress -> Human Review` через fresh Codex session.
7. Переход `Human Review -> Rework -> In Progress` в новой fresh session с сохранением branch и workspace.
8. Проверку branch и disposable PR linking по Linear identifier.
9. Проверку receipt reuse для exact unchanged inputs и invalidation после commit, lock или environment identity change.
10. Проверку `Human Review -> Merging`, merge preconditions и возврата в `Rework` при candidate mutation.
11. Запуск review после implementation blockers и acceptance после review blockers.
12. Создание remediation blocker из finding, возврат review или acceptance в blocked `Todo` и повторный fresh full pass после fix.
13. Terminal transition и idempotent local cleanup для `Done` и `Canceled`, включая linked open PR у canceled task.
14. Выполнение required project-resource cleanup node и Project transition в `Completed` только после final acceptance.
15. Проверку concise attempt telemetry и complete local phase baseline без prompt, secret или raw-log leakage.

Acceptance обязана доказать, что новый clean Codex thread может выбрать одну ready issue, восстановить весь необходимый context только из Linear, Git/GitHub и stable source contracts и довести task до её следующего human или terminal boundary. Наличие Symphony, AWS resource или brainstorm chat history запрещено как prerequisite.

### Terminal Completion Audit

После предполагаемого завершения заново проверить полный current scope против `goal.md`, этой specification, всех changed stable owners, installed local plugins, Linear configuration и real integration state. Любая incomplete, contradictory, stale или unverified requirement исправляется, affected verification повторяется, затем полный audit начинается заново. Goal завершается только после нового полного прохода без findings.
