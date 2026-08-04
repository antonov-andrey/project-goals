# Стандартизация project tooling во всех проектах

## Требуемый результат

Все canonical repositories под `/home/andrey/Projects` используют одну модель instructions, stable design, временных task artifacts и reusable skills. Общие task workflows имеют одного владельца в installable plugin `agent-workflows` из marketplace repository `agent-plugins`; reusable workflow-container procedures принадлежат отдельному installable plugin `workflow-container-agent-tools` того же marketplace; явно утверждённые пользователем reusable marketplace procedures, domain instructions и domain tools принадлежат отдельному installable plugin `marketplace-agent-tools` того же marketplace; личные инженерные стандарты имеют отдельного владельца в repository и installable plugin `project-standards`. Consumer projects не содержат локальных копий этих reusable assets, но сохраняют явно оставленные пользователем project-specific skills.

Current repository `workflow-container-tools` переходит к целевой роли marketplace repository `agent-plugins`. Current installable plugin `workflow-container-tools` переименовывается в `workflow-container-agent-tools`, новый installable plugin `agent-workflows` получает утверждённые общие workflows, `goal-brainstorm` переходит из domain plugin в `agent-workflows`, а новый installable plugin `marketplace-agent-tools` получает только явно назначенные ему пользователем marketplace-specific workflows, instructions и tools. Новый repository, marketplace и installable plugin получают имя `project-standards`. Имя `project-tools` не используется. Общий префикс для repository names откладывается до утверждения окончательного имени основного проекта; domain plugin identifiers уже используют обязательный общий suffix `-agent-tools`.

## Подтверждённое текущее состояние

24 июля 2026 года workspace содержит 20 верхнеуровневых git checkout. Восемнадцать checkout с доступными GitHub remotes образуют полный existing canonical repository scope этой миграции. `template-bin` является отдельным approved retirement target по `Batch 37`, а clean checkout `marketplace-tools-ml` с уже удалённым GitHub remote является локальным остатком вне scope и не удаляется этой goal. После retirement и создания нового отдельного repository `project-standards` final canonical inventory содержит 19 repositories.

GitHub repository и local checkout `compose-codex-app-server` уже удалены пользователем. Из утверждённого retirement scope остаётся только exact Docker cleanup по `Batch 36`: остановленный container, image, legacy volume и пустая network.

GitHub repositories `antonov-andrey-org/workflow-source-codex` и `antonov-andrey-org/submodule-anthropic-agent` уже удалены пользователем. `workflow-source-codex` не имел local checkout, gitlink или tracked consumer reference и не требует implementation cleanup. `marketplace-next-co-uk` всё ещё содержит stale gitlink `anthropic_agent` удалённого repository; exact cleanup принадлежит `Batch 38`.

Корневой `AGENTS.md` присутствует в 12 из 18 existing canonical repositories и отсутствует в 6. `workflow-control-center/AGENTS.md` и `marketplace-tr-priority/AGENTS.md` содержат соответственно 1479 и 1347 строк и объединяют общие инструкции, project-specific contracts, path ownership и workflow protocols. `marketplace-infrastructure/AGENTS.md` и `marketplace-tools/AGENTS.md` также смешивают устойчивую архитектуру с инженерными инструкциями.

Сравнение двух самых больших `AGENTS.md` выявило 1308 общих строк по longest common subsequence, что составляет 88% файла `workflow-control-center` и 95% файла `marketplace-tr-priority`. Различия включают не только project-specific дополнения, но и расходящиеся общие контракты, включая ORM ownership и table lifecycle. Поэтому ни один из этих файлов нельзя считать случайной расширенной копией другого или автоматически заменить общим сокращённым шаблоном.

Project-local skills находятся только в двух canonical repositories:

- `workflow-control-center` содержал 12 skills;
- `marketplace-tr-priority` содержал 19 skills.

Все 12 skill names из `workflow-control-center` присутствовали в `marketplace-tr-priority`. Десять соответствующих skill directories были идентичны, а `code-audit` и `instruction-audit` разошлись вместе с частью supporting assets. Оба проекта содержали одинаковый `.codex/config.toml` и копии общего `.codex/agents` orchestration tree.

Tracked `doc/spec/**` и `doc/goal/**` существовали только в `workflow-container-tools` и `workflow-control-center`. Ни один checkout не использовал `.spec/`. Девять Python-проектов уже имели общее PyInstaller ignore-правило `*.spec`, которое не является владельцем task artifact contract.

Stable design находился под `doc/design.md` или `doc/design/**` в нескольких проектах и смешивался с operations и task history. Формального единого `DESIGN.md` contract не было.

## Scope

Миграция охватывает следующие 18 existing canonical repositories:

- `arasta-link-docs`;
- `brand-size-chart`;
- `browser-runtime`;
- `compose-milvus`;
- `compose-mysql`;
- `marketplace-infrastructure`;
- `marketplace-next-co-uk`;
- `marketplace-parser`;
- `marketplace-tools`;
- `marketplace-tr`;
- `marketplace-tr-priority`;
- `scrapy-next-deprecated`;
- `v0-arasta-link`;
- `vpn-runtime`;
- `workflow-container-contract`;
- `workflow-container-runtime`;
- `workflow-container-tools`;
- `workflow-control-center`.

Новый repository `antonov-andrey/project-standards` создаётся как девятнадцатый final canonical repository и provider по разделу `Provider project-standards`.

Scope включает:

- marketplace repository `agent-plugins`, installable plugins `agent-workflows`, `marketplace-agent-tools` и `workflow-container-agent-tools`, их skills, references, templates, tools и tests;
- отдельный repository, marketplace и installable plugin `project-standards`, его capability skills, audit cards, mechanical checkers и tests;
- глобальную пользовательскую Codex configuration и global instructions как Codex adapter общего стандарта;
- project-local `AGENTS.md`, harness configuration и agent assets;
- `DESIGN.md`, `design/**`, `docs/**`, старые `doc/design*`, `doc/spec/**` и `doc/goal/**`;
- `.gitignore` и harness-neutral `.spec/`;
- механическое workspace discovery, standard selection validation и provider availability checks;
- сохранение project-specific domain behavior при удалении локальных skill wrappers.

## Non-goals

Миграция не изменяет application behavior, production data, production infrastructure, business schemas, public product APIs или runtime versions только ради унификации. Она не модернизирует legacy code и не заставляет проекты использовать один язык, framework, Python version или test runner. По отдельному прямому требованию пользователя разрешено исправить `bin/venv_create.sh` в `marketplace-tools`, `marketplace-next-co-uk`, `marketplace-parser` и `marketplace-tr`, чтобы проверочный environment воспроизводимо создавался project-owned script на уже объявленном runtime Python `3.14`; это не является повышением runtime version. Скрипты проверяют только major/minor `3.14`, не закрепляют patch release вроде `3.14.5`, используют latest доступный `3.14` и не изменяют dotenv loading или application bootstrap.

По прямому требованию пользователя от 24 июля 2026 года существующая семантика загрузки runtime environment в `Legacy` consumers находится вне scope этой goal. В таких consumers запрещено ради provider convergence изменять `load_dotenv()` или `config_env` calls, набор, порядок и `override` semantics dotenv-файлов, environment keys, application bootstrap, а также gitlinks и call sites `config_env`, `config_logging` и `config_sqlalchemy`, если их обновление требует такого изменения. Явно утверждённые `Batch 24`, `Batch 25` и `Batch 26` разрешают только перечисленные в них consumer cutover, включая точечно названные `Legacy` owners, при полном сохранении этой environment/bootstrap-семантики и project-owned verification фактического поведения. Любой другой `Legacy` consumer, включая `scrapy-next-deprecated`, остаётся вне provider convergence scope и требует нового явного запроса пользователя.

Пустые `DESIGN.md`, `design/`, `docs/` и `.spec/` не создаются ради симметрии. Никакой checkout, branch, repository, remote или пользовательский артефакт не удаляется без отдельного подтверждённого lifecycle owner.

Эта specification явно разрешает agent создать `antonov-andrey/project-standards`, переименовать `antonov-andrey/workflow-container-tools` в `antonov-andrey/agent-plugins` и удалить `antonov-andrey-org/template-bin` после выполнения соответствующих dependency и verification gates. Другие commit, push, repository creation, remote rename, repository deletion и внешние публикационные действия требуют отдельного exact lifecycle approval.

## GitHub lifecycle execution

Все новые repositories этой goal создаются у personal GitHub owner `antonov-andrey`. Existing repositories сохраняют current owner при rename; создание нового repository вместо rename запрещено, когда target является прямым lifecycle continuation существующего repository.

Перед creation или rename agent:

- проверяет authenticated GitHub identity и доступность exact target full name;
- не выводит token или другие credentials;
- создаёт либо переименовывает только exact repository из этой specification;
- выполняет полный atomic rename cutover по `DESIGN.md`, section `GitHub Repository Lifecycle`, включая canonical Git URLs, local checkout, worktree, marketplace-source, plugin-source и cache paths;
- устанавливает canonical `origin`, default branch и target repository identity во всех provider metadata, consumer references и global harness configuration;
- выполняет standalone provider verification до consumer cutover;
- после push доказывает совпадение local `HEAD`, canonical `origin` и published default branch.

Current GitHub CLI token имеет scope `repo`, достаточный для creation, push и repository administration, но не имеет scope `delete_repo`. Перед первым разрешённым remote deletion agent проверяет scopes через `gh auth status` и при необходимости выполняет authenticated `gh auth refresh -h github.com -s delete_repo`; deletion запрещён до подтверждённого появления scope. Authorization state не сохраняется в repository artifacts.

Перед deletion agent:

- проверяет exact approved full repository name и соответствующий batch gate;
- доказывает отсутствие active gitlinks, configured remotes, code, documentation, deployment и data dependencies;
- удаляет только exact repository без prefix или bulk selection;
- подтверждает отсутствие repository через authenticated GitHub lookup и отсутствие surviving active workspace references;
- не создаёт archive, mirror или forwarding replacement, если batch явно этого не требует.

Уже удалённые пользователем `antonov-andrey-org/workflow-source-codex` и `antonov-andrey-org/submodule-anthropic-agent` не проходят повторную deletion attempt. Их remaining local cleanup и no-recreate semantics принадлежат `Batch 38`.

## Утверждённая модель владельцев

`DESIGN.md` является stable source contract этой миграции. Каждый consumer project следует модели `Владельцы проектных артефактов`, `Design-документы`, `Harness-neutral task artifacts`, `Agent Plugins Marketplace`, `Project Standards`, `Project-local boundary`, `Конфигурация` и `Проверка`.

Reusable opinionated engineering standards принадлежат capability skills в `project-standards`. Project `AGENTS.md` явно объявляет required standards и остаётся владельцем concrete structure, owner paths, runtime versions, commands, security boundaries, product-specific constraints и local overrides. Если существующая или новая project entity, technology, boundary, artifact family или workflow уже имеет применимый capability skill в `project-standards`, этот skill обязательно указывается в `Required Standards` и применяется в соответствующей области. Отказ от применимого skill или переопределение этого выбора допустимы только по явному требованию пользователя; project-local convenience, молчаливое решение агента или отсутствие записи в текущем `AGENTS.md` не являются разрешением. Standard prose не материализуется и не копируется в generated-блок `AGENTS.md`. Недоступность required provider или skill запрещает изменение project до восстановления provider.

Другой harness использует adapter к тем же canonical provider contracts. Harness portability не создаёт consumer copy standards или workflows.

Global Codex `AGENTS.md` хранит только личные cross-project collaboration rules. Global Codex `config.toml` хранит общие runtime defaults. Project `.codex/config.toml` остаётся только при реальном project-specific отличии.

## Provider agent-plugins

Target marketplace repository получает GitHub identity `antonov-andrey/agent-plugins` посредством rename существующего `antonov-andrey/workflow-container-tools` и содержит три независимо устанавливаемых plugins. Rename выполняется после local provider verification и как единая операция с coordinated remote и local-path cutover.

Target layout:

```text
agent-plugins/
  .agents/plugins/marketplace.json
  plugins/
    agent-workflows/
      .codex-plugin/plugin.json
      skills/
      lib/
    marketplace-agent-tools/
      .codex-plugin/plugin.json
      skills/
    workflow-container-agent-tools/
      .codex-plugin/plugin.json
      skills/
  test/
```

Plugin `agent-workflows` получает и канонизирует:

- `code-antipattern-audit`;
- `code-audit`;
- `code-writer`;
- `explain-algorithm`;
- `explain-interface`;
- `explain-internal-api`;
- `explain-internal-map`;
- `explain-persistence`;
- `git-commit`;
- `goal-brainstorm`;
- `goal-review`;
- `instruction-audit`;
- `instruction-writer`;
- `sequential-batch`.

Plugin `workflow-container-agent-tools` сохраняет:

- `workflow-container-audit`;
- `workflow-container-developer`;
- `workflow-container-input-create`.

Plugin `marketplace-agent-tools` получает только явно назначенные пользователем reusable marketplace-specific workflows, instructions и tools, включая уже утверждённый `ozon-seller-api-developer`. Автоматический перенос по consumer count, domain vocabulary или потенциальной полезности запрещён. Generic implementation standards, generic agent workflows, project-specific marketplace skills и workflow-container domain contracts в него не копируются. Имя не совпадает с существующим application repository `marketplace-tools`.

`goal-brainstorm` удаляется из current plugin `workflow-container-tools` во время его переименования в `workflow-container-agent-tools` только после доказанной доступности canonical copy этого skill в `agent-workflows`.

Разошедшиеся `code-audit` и `instruction-audit` не выбираются по принципу одного случайного source repository. `agent-workflows` получает общий scope, orchestration, report и handoff contract; reusable opinionated checklist cards и mechanical standard checks принадлежат выбранным `project-standards` skills; product-specific checks остаются у project-local owners.

`code-antipattern-audit` сохраняет в `agent-workflows` multi-perspective procedure и deterministic result merge. Python-specific opinionated cards and checkers переходят к `project-standards:python-developer`.

Общие `.codex/agents` explain, section-audit, sequential-batch и subagent-transport protocols and utilities становятся plugin-local support assets `agent-workflows`; public `agent-workflows:sequential-batch` предоставляет stable provider-qualified dependency project-local workflows без ссылки на plugin cache path. `B7-2 Subagent config и role contracts` владеет exact Markdown owners трёх текущих named-agent TOML и их cutover contract. Target provider и consumer projects не содержат named-agent TOML.

`codex-exec-workflow` и `brand-size-chart-load` не переходят в `agent-workflows`, domain plugin или другой provider. `Batch 31` удаляет оба skill и весь доказанно изолированный старый `marketplace-tr-priority` brand-size-chart execution contour без successor skill: product control plane принадлежит `workflow-control-center`, а workflow domain runtime принадлежит отдельному project `brand-size-chart`.

Current `workflow_container_tools` Python discovery package удаляется без replacement. Workspace-wide inspection не нашёл внешних imports, CLI calls или других consumers: package используется только собственными tests и README, а plugin installation его не устанавливает.

## Provider project-standards

Создаётся отдельный GitHub repository `antonov-andrey/project-standards`, marketplace source и installable plugin `project-standards`.

Target layout:

```text
project-standards/
  .agents/plugins/marketplace.json
  plugins/
    project-standards/
      .codex-plugin/plugin.json
      skills/
  test/
```

Initial capability skill set:

- `project-foundation`;
- `project-instruction-developer`;
- `project-documentation-developer`;
- `python-developer`;
- `legacy-python-maintainer`;
- `python-cli-developer`;
- `python-logging-developer`;
- `python-retry-developer`;
- `pytest-developer`;
- `sqlalchemy-developer`;
- `runtime-config-developer`;
- `http-api-client-developer`;
- `rest-api-server-developer`;
- `typescript-developer`;
- `react-ui-developer`;
- `submodule-developer`;
- `docker-compose-developer`;
- `kubernetes-developer`;
- `aws-cloudformation-developer`;
- `project-standardize`;
- `project-standard-audit`.

Каждый capability skill владеет одним reusable standard, применимыми development rules, semantic audit cards, mechanical checkers и owner-local tests. Общие workflow mechanics не дублируются в этих skills и используются из `agent-workflows`.

`project-standards` не владеет marketplace-specific или workflow-container-specific domain logic. Domain plugins применяют generic engineering contracts из `project-standards` через exact provider references вместо их копирования.

`project-standardize` принимает явный workspace root, обнаруживает git repositories и их metadata, классифицирует required standards, сохраняет project-local overlay и предоставляет read-only check mode. Классификация сравнивает фактические project entities, technologies, boundaries, artifact families и workflows с областями применимости доступных capability skills; пропуск применимого skill является validation failure, если для него не записано явное требование пользователя об исключении. Generic implementation не содержит список текущих repositories или `/home/andrey/Projects`.

Project `AGENTS.md` выбирает capability skills в canonical section `Required Standards`. Отдельный project-standard manifest и generated standard block не создаются без доказанной machine-readable потребности. Project-local text не копирует standard prose.

## Project-specific skill migration

Project-specific skills не публикуются глобально под случайными domain names.

В `marketplace-tr-priority`:

- `brand-size-chart-load` и `codex-exec-workflow` удаляются без replacement skill вместе с доказанно изолированным старым brand-size-chart execution contour по `Batch 31`;
- `ozon-attribute-map`, `ozon-data-contract` и `ozon-dimension-reference` полностью остаются project-local skills вместе со своими owner-local assets и project data contracts по `Batch 35`;
- `ozon-seller-api-workflow` переходит в `marketplace-agent-tools:ozon-seller-api-developer`, stable provider contract остаётся у `ozon_seller_api`, а общий submodule publication workflow используется из `agent-workflows:git-commit`;
- полный skill `trendyol-size-chart-parse`, включая его project-specific instructions, references, tools, payload contract и curated data, остаётся project-local в `marketplace-tr-priority`; только generic batch и subagent mechanics получает из `agent-workflows:sequential-batch`;
- действующий Trendyol-to-Ozon size pipeline и его persisted product-size entities защищены от удаления старого brand-size-chart contour точным contract из `Batch 31`.

После успешного cutover consumer repositories не содержат consumer copies reusable provider skills, generic `.codex/agents`, generated copies standards или альтернативный install path. Явно сохранённые пользователем project-specific skills остаются под project-local `.codex/skills/**` вместе со своими owner-local assets.

## Project instruction standards

Project обязан использовать все независимо комбинируемые capability skills, применимые к его реальному составу. Initial coverage включает:

- modern Python;
- legacy или maintenance Python;
- Python CLI;
- Python logging;
- pytest;
- SQLAlchemy;
- runtime configuration;
- external HTTP API clients;
- REST API servers;
- React и TypeScript;
- Docker Compose;
- Kubernetes;
- AWS и CloudFormation infrastructure;
- documentation и instruction artifacts;
- Git submodules.

Каждый canonical repository получает корневой `AGENTS.md`, который определяет purpose/status, `Required Standards`, runtime versions, key owner paths, build/test/lint/run commands, external side effects, security boundaries, verification contract и минимальный project overlay.

Standard selection следует real current project state и является обязательным, а не рекомендательным. Появление новой entity, technology, boundary, artifact family или workflow, уже описанной одним capability skill, требует добавить и применять этот skill в том же change set. Не применять или заменить применимый capability skill можно только по явному требованию пользователя, которое должно быть сохранено как project-local exception; агент не может вывести такое исключение из convenience, существующего несоответствия или отсутствия прежней записи. Сама migration не повышает runtime version и не переносит legacy owner roots в modern structure. Несколько capability skills могут применяться одновременно.

Текущие `Worker script` и `base_worker` остаются project-local concepts `workflow-control-center`: только этот protected project содержит реальные long-running worker entrypoints и consumer-код `base_worker`. Они не переносятся в `project-foundation`, `python-developer` или другой reusable capability skill только на основании одного consumer. `marketplace-tr-priority` не содержит `Worker script`, поэтому его final instruction model не должен сохранять неприменимый worker-runtime contract. После отдельного явного подтверждения пользователя неиспользуемый `base_worker` submodule удаляется из `marketplace-tr-priority` сразу как самостоятельное lifecycle cleanup.

Размер `AGENTS.md` не является самостоятельной acceptance metric. Уменьшение файла допустимо только как результат утверждённого переноса, устранения точного семантического дубля или отдельно согласованного изменения требования.

`workflow-control-center/AGENTS.md` и `marketplace-tr-priority/AGENTS.md` являются protected instruction migrations. `project-standardize` не изменяет их автоматически и не выбирает один файл как source of truth для другого. До любых изменений этих файлов отдельный brainstorm формирует полный source-to-target ledger нормативных правил и классифицирует каждое предлагаемое преобразование как:

- перенос без изменения смысла;
- изменение формулировки с сохранением смысла;
- объединение доказанно эквивалентных дублей;
- реальное смысловое сокращение или удаление.

Для каждого review batch пользователь до редактирования получает исходное правило, proposed owner и path, предлагаемую формулировку, классификацию преобразования и обоснование сохранения или изменения смысла. Любой batch, уменьшающий, объединяющий или переформулирующий существующий contract, требует отдельного явного подтверждения пользователя. Ни semantic audit после изменения, ни общий workspace approval не заменяют это подтверждение.

Утверждённая начальная owner structure использует root `AGENTS.md` для project-local repository-wide contracts и выбора external standards, scoped nested `AGENTS.md` для настоящих subtree rules, capability skills `project-standards` для reusable opinionated rules и `DESIGN.md` с тематическими design-документами для stable architecture и domain contracts. Конкретное распределение правил по owners определяется только через approved ledger. Root-файл может стать короче, но полный набор обязательных rules не сокращается молча.

Capability skills получают только те rules из protected migrations, для которых утверждены reusable семантика и область действия. Product-specific names and contracts остаются project-local независимо от textual similarity. Расходящиеся common contracts остаются явными project overlays, пока отдельный brainstorm не утвердит их унификацию. `Approved protected instruction ledger` является закрытым allowlist реализации; преобразования вне него запрещены.

## Approved protected instruction ledger

### Batch 1: foundation writing, execution and subagent contracts

Пользователь явно утвердил этот batch 23 июля 2026 года. Источники `workflow-control-center/AGENTS.md` и `marketplace-tr-priority/AGENTS.md` остаются неизменными до закрытия полного protected ledger и provider-first implementation.

- `B1-1 Writing Rules`
  - sources: section `Writing Rules` в обоих protected `AGENTS.md`;
  - source state: sections byte-identical, section SHA-256 `c288c698c2b3165d6bdbaa60a02e0b267e54fc24f4e4c6ba67e1840efb13d4f9`;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/writing-and-reporting.md`;
  - transformation: перенос без изменения смысла или текста;
  - approved target wording: точная source section целиком.
- `B1-2 Language Zones`
  - sources: section `Language Zones` в обоих protected `AGENTS.md`;
  - source state: sections byte-identical, section SHA-256 `4301530a5d9e3e42797ed5160802c42e323de9d641ab2ef0f00e9e34746b6c4e`;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/writing-and-reporting.md`;
  - transformation: изменение формулировки после утверждённой documentation path migration и удаление отдельной language zone для отсутствующего в target repositories `pattern/**`;
  - approved target wording: source section целиком, кроме `Documentation prose contract`, который заменяется на:

```md
- Documentation prose contract:
  - Prose in `DESIGN.md`, `design/**`, and `docs/**` MUST use Russian and follow `Russian-language artifact contract`.
```

- `B1-3 User Change Protection Rules`
  - sources: section `User Change Protection Rules` в обоих protected `AGENTS.md`;
  - source state: sections byte-identical, section SHA-256 `b379b6f0dc233f8846c1ee78edcd5c9a29f95b3cbce2335067d2ee79754feb67`;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/execution.md`;
  - transformation: перенос без изменения смысла или текста;
  - approved target wording: точная source section целиком.
- `B1-4 Honest Execution Rules`
  - sources: section `Honest Execution Rules` в обоих protected `AGENTS.md`;
  - source state: sections byte-identical, section SHA-256 `10128bac3629bd30680e1987ffef201e81687b4e1b99e888bad90ff9e53762d7`;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/execution.md`;
  - transformation: перенос без изменения смысла или текста;
  - approved target wording: точная source section целиком.
- `B1-5 Problem Reporting Rules`
  - sources: section `Problem Reporting Rules` в обоих protected `AGENTS.md`;
  - source state: WCC SHA-256 `558f18451c97e7ab7ee6b10e73106e33414b6563e14835cba002841d7e966785`; MTP SHA-256 `df50c97c35793f0a91c0456dde0e6f3506a5fe13c280ad738d92215d15c824c1`;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/writing-and-reporting.md`;
  - transformation: MTP wording переносится без изменения; WCC получает явно утверждённое semantic strengthening;
  - approved target wording: точная MTP source section целиком, включая требования одного unambiguous recommended correction path и размещения alternatives только после него.
- `B1-6 Subagent Orchestration Rules`
  - sources: section `Subagent Orchestration Rules` в обоих protected `AGENTS.md`;
  - source state: sections byte-identical, section SHA-256 `3710bd2956a17c00f5faf2d9cd1b7be2b363468c00cb48282413b5e4e4024725`;
  - target owner: `agent-plugins/plugins/agent-workflows/lib/subagent-transport/protocol.md`;
  - transformation: изменение owner reference с сохранением runtime semantics;
  - approved target wording: source section целиком, кроме current project-local owner-reference bullet, который заменяется на:

```md
- `agent-workflows` plugin support owner `lib/subagent-transport/protocol.md` owns parent-side subagent transport, liveness tracking, and recovery mechanics.
```

  - preserved semantics: wait poll timeout `300000`, idempotent or restart-resumable parent tasks, closure of obsolete subagents, `direct_agent`, `agent_pool` и обязательное объявление transport mode.

### Batch 2: repository references, documentation and script workflow owners

Пользователь явно утвердил этот batch 23 июля 2026 года. Он разделяет прежний общий documentation block на reference-policy standard, documentation standard, reusable Python workflow pattern и project-local script catalog contract.

- `B2-1 Repository Reference Rules`
  - sources: section `Repository Reference Rules` в обоих protected `AGENTS.md`;
  - source state: sections отличаются только первым example path; WCC SHA-256 `46061b22f1d54ed78cb3deaa1b15d93f8810dbd447f90f7a21b086218be08d13`; MTP SHA-256 `fcc82f369a6257c4d6186899548b4e6bfeeaec434f7d919f96a727103efb022a`;
  - target owner: `project-standards/plugins/project-standards/skills/project-instruction-developer/references/repository-reference.md`;
  - transformation: сохранение действующей semantics, обновление target paths и semantic extension для external plugin contracts;
  - approved target wording:

```md
### Repository Reference Rules

- Repository-local references MUST use plain root-relative paths such as `test/code/test_reference_policy.py`, `design/backend.md`, or `docs/script_catalog.md`.
- Root-relative means relative to the owning repository boundary.
- Repository-local Markdown links are forbidden, except same-file heading-anchor links inside an explicit table of contents.
- Bare local relative references such as `./<local>.md` or `../<other>.md` are forbidden.
- A `template path` is an owner-local template path such as `plugins/<plugin>/skills/<skill>/template/**`, `plugins/<plugin>/lib/<owner>/template/**`, or an explicitly mapped project-local template owner.
- References from repository-local templates to repository-local instruction artifacts MUST use the consumed instruction artifact path.
- References from other repository-local artifacts to repository-local instruction artifacts MUST use the consumed instruction artifact path.
- External plugin contracts MUST be referenced by plugin name together with their canonical skill name or plugin-relative owner path; consumer-local substitute paths are forbidden.
- Template sources that are not instruction artifacts MUST be referenced by their template paths.
- A reference to a heading-defined owner MUST use the heading's exact literal text in backticks.
- When that owner lives in another repository-local file, the reference MUST also name its root-relative owning file.
- Paraphrased references are forbidden when the literal target heading already exists.
```

- `B2-2 Documentation Scope And Workflow`
  - sources: section `Documentation Scope And Workflow` в обоих protected `AGENTS.md`;
  - source state: sections byte-identical, section SHA-256 `90d829687ed463ecf5a71629535a01b40bc472302cc1da39167f5f884a785dcc`;
  - target owner: `project-standards/plugins/project-standards/skills/project-documentation-developer/references/documentation.md`;
  - transformation: разделение old `doc/**` owner согласно approved documentation architecture и удаление отдельного `pattern/**` root; project-specific implementation contracts маршрутизируются к существующим design или instruction owners, reusable cross-project patterns — к применимому capability skill;
  - approved target wording:

```md
### Documentation Scope And Workflow

- Stable project architecture and domain design belong in `DESIGN.md` or `design/**`.
- User, operational, and other maintained documentation that is not a stable design contract belongs under `docs/**`.
- A project-specific implementation contract belongs in `DESIGN.md` or `design/**` when it defines stable architecture or domain design, and in `AGENTS.md` when it defines durable normative engineering instructions.
- A reusable cross-project implementation pattern MUST belong to its applicable `project-standards` capability skill instead of being copied into consumer projects.
- Code changes MUST update documentation only when they change a stable documented architecture, operational fact, script lifecycle rule, run or test exception, or maintained project workflow.
- Documentation updates required by a code change MUST happen before handoff verification.
```

- `B2-3 Script Workflow Owner Pattern`
  - sources: `pattern/script_workflow_owner.md` и section `Pattern Documentation Rules` в обоих protected repositories;
  - source state: pattern files byte-identical, file SHA-256 `1888ce2879f09132733a8cfb9e6dac73495f7fef60c3805dfeb24882dda7739e`; rule sections byte-identical, section SHA-256 `a8473e5e81db614bbf8e7982c3113baa1496a454056598f47611594558e4080a`;
  - target owner: `project-standards/plugins/project-standards/skills/python-developer/references/script-workflow-owner.md`;
  - transformation: перенос reusable pattern без изменения algorithmic semantics и удаление consumer `pattern/**` directories после provider cutover, поскольку других файлов в них нет;
  - approved target pattern wording: точный source file целиком, кроме первого sentence в `Purpose`, который заменяется на `This reference describes how to structure one bounded runtime algorithm when it is split into ordered stages.`;
  - approved capability contract:

```md
- `references/script-workflow-owner.md` owns the reusable implementation pattern for one bounded runtime algorithm split into ordered stages.
- Apply that pattern when non-`Legacy` project code implements a bounded algorithm with two or more ordered stages and its correctness depends on the applicability conditions declared by that reference.
```

- `B2-4 Script Catalog Rules`
  - sources: section `Script Catalog Rules` в обоих protected `AGENTS.md`;
  - source state: sections byte-identical, section SHA-256 `943da508701e75b7029db136366b4a7adfe49c1c93d9501acf6e86e195d0e71e`;
  - standard owner: `project-standards/plugins/project-standards/skills/project-documentation-developer/references/script-catalog.md`;
  - project-local data owner: `docs/script_catalog.md`;
  - transformation: source section переносится без изменения semantics, каждое `doc/script_catalog.md` заменяется на `docs/script_catalog.md`;
  - preserved project-local boundary: concrete script purpose, pipeline position, run or test differences и script-specific safety остаются в catalog owning project.

### Batch 3: instruction protocol and mandatory standard applicability

Пользователь явно утвердил этот batch 23 июля 2026 года с semantic strengthening: применимый capability skill является обязательным при наличии или появлении уже описанной им project entity, technology, boundary, artifact family или workflow; отказаться от этого выбора или переопределить его можно только по явному требованию пользователя.

- `B3-1 Instruction Protocol`
  - sources: полный section `Instruction Protocol` в обоих protected `AGENTS.md`, включая `Instruction Protocol Terms`, `Section Placement Rules`, `Cross-Definition Rules`, `Core Terms Rules`, `Standard Submodule List Rules`, `Key Directory Map Rules`, `Allowed Dependency Matrix Rules`, `Repository-wide Definition Rules` и `Main Project AGENTS.md Structure Rules`;
  - source state: sections byte-identical, section SHA-256 `6129b61654e0986912d359128025219ff5296397774d97b69d31b1650bf70eb7`;
  - target owner: `project-standards/plugins/project-standards/skills/project-instruction-developer/references/instruction-protocol.md`;
  - transformation: изменение owner context с сохранением всех девяти source contracts без смыслового сокращения;
  - approved target wording: точный source section целиком со следующим закрытым набором замен:
    - `this file` заменяется на `the governed AGENTS.md file`;
    - `this document` заменяется на `the governed AGENTS.md instruction model`;
    - `this repository` заменяется на `the governed project repository`;
    - `promoted here` заменяется на `promoted in the governed AGENTS.md`;
    - `instruction-audit` как workflow owner заменяется на `agent-workflows:instruction-audit`;
    - `doc/script_catalog.md` заменяется на `docs/script_catalog.md`;
    - repository-local owner wording для самого `Instruction Protocol` заменяется на provider owner `project-standards:project-instruction-developer`;
  - forbidden transformations: другие переформулировки, объединения, сокращения или удаления исходных правил не утверждены.
- `B3-2 External Standard Reference Rules`
  - source state: новый provider boundary, отсутствующий в protected source files и необходимый для утверждённой external standard model;
  - target owner: `project-standards/plugins/project-standards/skills/project-instruction-developer/references/instruction-protocol.md`;
  - transformation: semantic extension, явно утверждённый пользователем;
  - approved target wording:

```md
### External Standard Reference Rules

- `Required Standards` is the single project-local owner of the external standards selected by one governed `AGENTS.md`.
- Each entry in `Required Standards` MUST name one provider-qualified skill and state its project applicability without copying the skill contract.
- Every governed project MUST declare:
  - `project-standards:project-foundation` for all project work;
  - `project-standards:project-instruction-developer` for reading, interpreting, auditing, or changing project instructions.
- When an existing or newly introduced project entity, technology, boundary, artifact family, or workflow already has an applicable provider standard, the corresponding provider-qualified skill MUST be added to `Required Standards` and applied to that scope in the same change set.
- Standard applicability MUST be derived from the current project state and task scope; it is a requirement, not an optional selection or recommendation.
- An applicable provider-qualified skill MAY be omitted, replaced, or declared inapplicable only when an explicit user requirement authorizes that exception.
- Agent inference, project-local convenience, existing non-compliance, or the absence of a previous `Required Standards` entry MUST NOT create such an exception.
- If one required provider or skill is unavailable, read-only discovery MAY continue, but project mutation MUST stop until the required owner is available.
- A project-local rule MAY narrow or specialize an external standard only when an explicit user requirement authorizes that specialization and the rule names the provider-qualified skill, exact external owner, and local scope.
- One authorized project-local specialization has precedence only inside its declared local scope.
- An unmarked contradiction between a project-local rule and a required external standard is invalid and MUST block project mutation until the conflict is resolved.
- Project-local `AGENTS.md` MUST NOT copy, paraphrase, generate, or materialize external standard prose.
```

- `B3-3 Domain Plugin Ownership Rules`
  - source state: новое cross-project instruction requirement, явно заданное пользователем 24 июля 2026 года;
  - target owner: `project-standards/plugins/project-standards/skills/project-instruction-developer/references/instruction-protocol.md`;
  - global agent integration: пользовательский global `AGENTS.md` применяет этот contract как обязательную ownership rule при создании, переносе или классификации reusable skills, references, templates и agent tools;
  - transformation: semantic extension, которая делает отдельный domain plugin обязательным владельцем reusable domain-specific agent assets;
  - approved target wording:

```md
### Domain Plugin Ownership Rules

- A reusable agent asset is domain-specific when its triggers, vocabulary, decisions, contracts, or tools depend on one business or platform domain and are not valid as a general task workflow or cross-domain engineering standard.
- Reusable domain-specific skills, references, templates, tools, and tests MUST belong to one independently installable plugin dedicated to that coherent domain.
- One coherent domain MUST use one canonical domain plugin instead of creating provider copies per consumer project, per repository, per vendor endpoint, or per task.
- A domain plugin MUST expose its reusable contract through one or more independently triggerable domain skills; an empty plugin or a plugin with no skill entrypoint is forbidden.
- When a reusable domain asset appears and a canonical plugin for that domain already exists, the asset MUST be added to that plugin.
- When a reusable domain asset appears and no canonical plugin for that domain exists, a domain plugin MUST be created before consumer-local copies are published or retained as the reusable owner.
- Classification of each skill, reference, template, or agent tool as project-local or reusable domain-specific MUST follow an explicit user-approved source-to-target decision; consumer count, potential future reuse, and agent inference MUST NOT make that decision.
- When the user classifies one asset as reusable domain-specific, that asset MUST move to the canonical domain plugin; when the user classifies it as project-specific, it MUST remain with its project owner.
- Generic task procedures and orchestration belong to `agent-workflows`.
- Cross-domain opinionated engineering standards belong to `project-standards`.
- Reusable workflow-container domain assets explicitly assigned by the user to a shared provider belong to `workflow-container-agent-tools`.
- Reusable marketplace domain assets explicitly assigned by the user to a shared provider belong to `marketplace-agent-tools`.
- A domain plugin MUST reference applicable generic workflow and engineering owners instead of copying their contracts.
- Stable runtime provider design remains in the provider repository `DESIGN.md`; application-specific business behavior, paths, configuration, data, and executable runtime logic remain with the owning project unless the domain plugin owns a real reusable agent tool.
- A domain plugin name MUST identify both its domain and agent-tool role and MUST NOT collide with an active application repository, marketplace source, or another plugin identifier.
- Every domain plugin identifier MUST use the common `<domain>-agent-tools` shape; a different suffix or a suffixless domain plugin identifier is forbidden unless the user explicitly changes this naming contract.
- If an applicable domain plugin or required domain skill is unavailable, read-only discovery MAY continue, but mutation of the governed domain scope MUST stop until the provider is available.
- Omitting, replacing, or bypassing an applicable domain plugin is allowed only by an explicit user requirement.
```

### Batch 4: external term resolution and foundational repository entities

Пользователь явно утвердил этот batch 24 июля 2026 года. Он разрешает governed `AGENTS.md` использовать named terms обязательного provider skill без consumer copy и переносит только четыре byte-identical определения, не требующие выбора между расходящимися project semantics.

- `B4-1 External Core Term Resolution`
  - source state: новый provider boundary, необходимый для использования provider-owned terms без копирования их definitions в consumer `AGENTS.md`;
  - target owner: section `External Standard Reference Rules` в `project-standards/plugins/project-standards/skills/project-instruction-developer/references/instruction-protocol.md`;
  - transformation: semantic extension, явно утверждённый пользователем;
  - approved target wording:

```md
- A `Core Terms` definition owned by a required provider-qualified skill is part of the governed `AGENTS.md` instruction model within that skill's declared applicability scope.
- A governed `AGENTS.md` MAY reference such a named term without copying its definition.
- The provider-owned term block remains the single authoritative and complete definition owner across the governed instruction model.
- Project-local `Core Terms` MUST contain only project-specific terms that are not already defined by an applicable required standard.
- A project-local `AGENTS.md` MUST NOT restate or redefine a provider-owned term.
- If two applicable required standards define the same term incompatibly, project mutation MUST stop until the provider ownership conflict is resolved.
- An explicitly user-authorized project-local specialization MAY extend the use of a provider-owned term inside its declared local scope, but MUST NOT become a second definition owner.
```

- `B4-2 Main project`
  - sources: term `Main project` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `342796d51f12ee04d41e26d29c5920dfbb53fb1e051342a75c483f907f3ec6fc`;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/repository-model.md`;
  - transformation: перенос без изменения смысла или текста;
  - approved target wording:

```md
- `Main project`
  - Meaning: the aggregate repository project entity for the whole repository root.
  - Membership: it includes `Main project code`, `Main project AGENTS.md`, `Main project documentation`, `Skill`, `Subagent config`, `Subagent group`, root `test`, root `tool`, `Submodule`, and other project-local assets such as `.codex/config.toml`, `log/**`, `Legacy`, data files, images, and text artifacts.
  - Aggregate role: `Main project` is the umbrella project term for the full repository and its member entities, not a replacement for the local ownership of those member entities.
```

- `B4-3 Self-contained`
  - sources: term `Self-contained` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `5c49b0fff9f88cbaa25aad472a10346a7a571dff7d3c09c28dba609c4f3d430a`;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/repository-model.md`;
  - transformation: перенос без изменения смысла или текста;
  - approved target wording:

```md
- `Self-contained`
  - Meaning: the entity owns its own private files and local assets within its own boundary.
  - Duplication boundary: duplication of code, instructions, tooling, tests, templates, or other owned assets across `Self-contained` entities is forbidden.
  - Shared-owner rule: when multiple `Self-contained` entities need the same long-lived asset or contract, promote it to one canonical owner instead of cloning it across entities.
```

- `B4-4 log`
  - sources: term `log` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `508731041f8e4a60e42813b2f51b9ddc32ae5926bad8d2a3b78fe27e233e1fdf`;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/repository-model.md`;
  - transformation: перенос без изменения смысла или текста;
  - approved target wording:

```md
- `log`
  - Meaning: the runtime log root family.
  - Root owner: `log/**` in the root repository.
  - Centralization rule: `Submodule` runs executed through the project centralize logs there.
  - Boundary: standalone `Submodule` logging belongs to the standalone `Submodule` contract.
```

- `B4-5 Main project AGENTS.md`
  - sources: term `Main project AGENTS.md` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `7efaa26de164b4412cda698a895dc55b93c38875644761739e6b9f541e84782b`;
  - target owner: `project-standards/plugins/project-standards/skills/project-instruction-developer/references/instruction-protocol.md`;
  - transformation: перенос без изменения смысла или текста;
  - approved target wording:

```md
- `Main project AGENTS.md`
  - Meaning: the repository-root canonical `AGENTS.md` file together with canonical nested `Main project AGENTS.md` files outside `Submodule` roots.
  - Applicability: `AGENTS.md` application is path-scoped, and the repository-root canonical `AGENTS.md` applies by default to the whole repository including `Submodule`s.
  - Precedence: for any path in the `Main project` repository boundary, apply every applicable canonical `Main project AGENTS.md` on the directory chain from the repository root to that path; each nearer file may only clarify, narrow, or specialize its applicable parent instructions for its own subtree and MUST NOT contradict them, and for paths inside one `Submodule` this applicable `Main project AGENTS.md` chain remains applicable but yields to the applicable `Submodule AGENTS.md` chain on conflict.
```

### Batch 5: reusable Python entities and project-local worker lifecycle

Пользователь явно утвердил этот batch 24 июля 2026 года. Первоначально предложенный `Worker script` provider extraction отменён по явному решению пользователя: concept остаётся вместе с `base_worker` в project scope `workflow-control-center`. Пользователь отдельно потребовал немедленно удалить неиспользуемый `base_worker` submodule из `marketplace-tr-priority`.

- `B5-1 Legacy`
  - sources: term `Legacy` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `cc8f401cd513f34887b241e4e5617d0e21f786cc43ca4e53dfa5f0fd11cf527a`;
  - target owner: `project-standards/plugins/project-standards/skills/legacy-python-maintainer/references/legacy-python.md`;
  - transformation: изменение documentation path с сохранением остальных source semantics;
  - approved target wording: точный source term block, где `doc/script_catalog.md` заменяется на `docs/script_catalog.md`.
- `B5-2 Python code`
  - sources: term `Python code` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `8aecb6c0c635cc2306bb48bd1a1a512fca019333cc19d54204f7501930a67c6c`;
  - target owner: `project-standards/plugins/project-standards/skills/python-developer/references/python-code.md`;
  - transformation: изменение repository и rule-owner context без изменения file scope;
  - approved target wording:

```md
- `Python code`
  - Meaning: all existing `.py` files in the governed project repository, including `Submodule`s, that are not ignored by `.gitignore`.
  - Repository-wide rules owner: `project-standards:python-developer` is the single provider owner of reusable repository-wide rules for `Python code` in the governed instruction model.
```

- `B5-3 Python script`
  - sources: term `Python script` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `a8366cbd6e8ef930995aae29992c94ff91198281b3977a8e1d8270bc276e9093`;
  - target owner: `project-standards/plugins/project-standards/skills/python-cli-developer/references/python-script.md`;
  - transformation: перенос без изменения смысла или текста;
  - approved target wording: точный source term block целиком.
- `B5-4 Worker script and base_worker`
  - sources:
    - term `Worker script` в обоих protected `AGENTS.md`, byte-identical SHA-256 `d4eb8e7ad0ed9bbeb0325f365dfcc09a070def0e9e3ee9a49853715461c33890`;
    - `base_worker` block в `marketplace-tr-priority/AGENTS.md`, SHA-256 `b64b0b7a97247911ba7ac56b7f5fb10a54739f3c2fc8e0563cb13c7c2adb0373`;
    - `base_worker/` tree line and explanatory bullet в `marketplace-tr-priority/AGENTS.md`;
    - `base_worker` applicability example в `marketplace-tr-priority/.codex/skills/code-audit/SKILL.md`;
    - `base_worker` gitlink and `.gitmodules` section в `marketplace-tr-priority`;
  - verified current state:
    - `workflow-control-center` содержит пять deployed long-running worker entrypoints и consumer-код `base_worker`;
    - `marketplace-tr-priority` не содержит `Worker script` или consumer-код `base_worker`;
  - `workflow-control-center` target owner: project-local root `AGENTS.md`; current `Worker script` и `base_worker` contracts сохраняются без изменения;
  - `marketplace-tr-priority` target state: `Worker script` term, `base_worker` standard block, Key Directory Map entries, audit applicability example, gitlink и `.gitmodules` section удаляются;
  - reusable provider target: отсутствует по явному решению пользователя; возможный будущий перенос требует нового явного решения пользователя, а не достижения заданного числа consumers;
  - transformation: явное project-scope разделение и semantic deletion неприменимого contract, отдельно одобренные пользователем; physical submodule deletion является явно запрошенным lifecycle cleanup.

### Batch 6: Submodule entity definitions

Пользователь явно утвердил этот batch 24 июля 2026 года. Все три source term blocks byte-identical и переносятся без contextual substitutions, сокращений или изменения semantics.

- `B6-1 Submodule`
  - sources: term `Submodule` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `f4af943e8cfb17d7376c62fc37be2306613a0f341136d10ecff37f51ecbf105a`;
  - target owner: `project-standards/plugins/project-standards/skills/submodule-developer/references/submodule-model.md`;
  - transformation: перенос без изменения смысла или текста;
  - approved target wording:

```md
- `Submodule`
  - Meaning: a git submodule root treated as a sibling repository entity with its own `Submodule code`, local `tool`, local `test`, `Submodule AGENTS.md`, and other local assets such as data files, images, and text artifacts.
  - Ownership: one `Submodule` owns its full local repository boundary and all entity-local assets inside that root.
```

- `B6-2 Submodule AGENTS.md`
  - sources: term `Submodule AGENTS.md` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `b2a8eba9f81734927511579f2723fc596a9b614491af41f79f2fab9832e658bd`;
  - target owner: `project-standards/plugins/project-standards/skills/project-instruction-developer/references/instruction-protocol.md`;
  - transformation: перенос без изменения смысла или текста;
  - approved target wording:

```md
- `Submodule AGENTS.md`
  - Meaning: every `AGENTS.md` file inside one `<submodule_root>/` tree, including `<submodule_root>/AGENTS.md` and any deeper nested `AGENTS.md`.
  - Boundary role: `Submodule AGENTS.md`, when present, defines the local boundary model inside that `Submodule`.
  - Scope limit: `Submodule AGENTS.md` must not redefine root repository ownership outside the `Submodule`.
  - Language override owner: `Submodule` language overrides belong to `Submodule AGENTS.md`.
  - Local precedence role: when `<submodule_root>/AGENTS.md` exists, it is the root of the local canonical `Submodule AGENTS.md` chain for paths inside that `Submodule`; otherwise the nearest applicable deeper canonical `AGENTS.md` inside that same `Submodule` starts the applicable `Submodule AGENTS.md` chain for its own subtree; nested canonical `AGENTS.md` files inside that same `Submodule` may only clarify, narrow, or specialize their applicable `Submodule AGENTS.md` parent and MUST NOT contradict it, and conflicts between the applicable `Main project AGENTS.md` chain and the applicable `Submodule AGENTS.md` chain for a path inside that `Submodule` MUST be resolved in favor of the `Submodule AGENTS.md` chain.
```

- `B6-3 Submodule code`
  - sources: term `Submodule code` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `44f65444f4c823525456c38dc8d5acb9219eeaaff12018e1a5f7daf22899c552`;
  - target owner: `project-standards/plugins/project-standards/skills/submodule-developer/references/submodule-model.md`;
  - transformation: перенос без изменения смысла или текста;
  - approved target wording:

```md
- `Submodule code`
  - Meaning: the code entity owned by one `Submodule` for its local runtime and business logic.
  - Membership: it includes reusable library code, persisted-entity code, script-family code, and submodule entrypoints when those entrypoints are runtime or business wrappers rather than standalone support utilities.
  - Exclusions: it does not include `Submodule AGENTS.md`, local `test`, local `tool`, or other non-code local assets of that `Submodule`.
  - Python-specific rules owner: `Submodule Code Rules` is the single owner of Python `Submodule code` rules that are narrower than the shared `Code` rule layer in this document.
```

### Batch 7: Skill и отказ от consumer-local .codex/agents

Пользователь явно утвердил этот batch 24 июля 2026 года после уточнения exact owners трёх текущих named-agent TOML. Canonical skill model переходит к provider-qualified identity, а reusable subagent contracts теряют consumer-local и Codex-specific path ownership.

- `B7-1 Skill`
  - sources: term `Skill` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `47fc78b5a61c005f99e8067a34ece245bb8c05a159dd1fbef3ded6b6dc8f241d`;
  - target owner: `project-standards/plugins/project-standards/skills/project-instruction-developer/references/skill-model.md`;
  - transformation: reusable provider skills переходят из consumer path `.codex/skills/**` в provider plugin path и получают provider-qualified identity; явно сохранённые пользователем project-specific skills сохраняют project-local path и identity, остальные ownership semantics сохраняются;
  - approved target wording:

```md
- `Skill`
  - Meaning: a `Self-contained` instruction entity rooted either at `plugins/<plugin_name>/skills/<skill_name>/` in its provider repository or at `.codex/skills/<skill_name>/` in the owning project when an explicit user-approved source-to-target decision retains that skill as project-specific.
  - Provider identity: consumers MUST reference one provider `Skill` by its provider-qualified `<plugin_name>:<skill_name>` identity; one retained project-specific `Skill` uses its project-local `<skill_name>` identity only inside its owning project.
  - Canonical contract: `plugins/<plugin_name>/skills/<skill_name>/SKILL.md` for one provider `Skill`, or `.codex/skills/<skill_name>/SKILL.md` for one retained project-specific `Skill`.
  - Frontmatter description: a `Skill` frontmatter description MUST start with `Use when ...` and MUST describe concrete task triggers in direct language.
  - Owner-local placement: any owner-local artifact of a `Skill` must live under that `Skill` root.
  - Owner-local assets: `test`, `tool`, and other `Skill`-local assets belong to that `Skill`.
  - Ownership limit: a `Skill` must not silently become a generic owner for unrelated repository-wide rules.
```

- `B7-2 Subagent config и role contracts`
  - sources:
    - term `Subagent config` в обоих protected `AGENTS.md`, byte-identical SHA-256 `ef8e6ab76577ecbfc493cdafa89fc2f829f0410a44aef39c13e84053b078a673`;
    - `code_antipattern_audit_instrumental.toml` в обоих protected projects, byte-identical SHA-256 `33b70f832cc414b854d36380e9aade9d5e095675a425953110b815b833e1793d`;
    - `code_antipattern_audit_semantic.toml` в обоих protected projects, byte-identical SHA-256 `9758721d790e64fb492cbcddefb326df43b9ed6ddd65f7a68995dd7c212cd1fd`;
    - `sequential_batch_worker.toml` в обоих protected projects, byte-identical SHA-256 `b54e90e5233a61d2f884df1e262a9bec5bfe5f4ae1af2375913d55f74398138d`;
  - target generic owner: `agent-plugins/plugins/agent-workflows/lib/subagent-role-contract.md`;
  - target concrete owners:
    - `agent-plugins/plugins/agent-workflows/skills/code-antipattern-audit/references/instrumental-role.md`;
    - `agent-plugins/plugins/agent-workflows/skills/code-antipattern-audit/references/semantic-role.md`;
    - `agent-plugins/plugins/agent-workflows/lib/sequential-batch/worker-role.md`;
  - transformation: term `Subagent config` и canonical `.codex/agents/<role>.toml` удаляются; role mission, scope, limits, evidence, artifact и handoff semantics переходят к указанным Markdown owners; harness-specific metadata и launch configuration не копируются и не устанавливаются глобально или в consumer projects;
  - approved generic target wording:

```md
- Every workflow-owned subagent role contract MUST state the role mission, scope, limits, evidence requirements, and handoff rules.
- A workflow MUST express that contract through its provider-owned task prompt or template and adapt execution to the available harness capabilities.
- Consumer-local named-agent TOML MUST NOT be the canonical or required cross-harness representation of a subagent role.
```

- `B7-3 Subagent group`
  - sources: term `Subagent group` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `7c1f4bb2738d8cd2fb796c11633fca0f553910e75a852c1dbbe5504d7cbab93d`;
  - target owner: path contract в root `AGENTS.md` repository `agent-plugins`;
  - transformation: global term и `.codex/agents/<group_name>/` path удаляются; ownership, isolation и owner-local asset semantics сохраняются в plugin-local path owner без введения нового глобального term;
  - approved target wording:

```md
- `plugins/agent-workflows/lib/<owner>/`: one `Self-contained` plugin-local owner for shared workflow or subagent protocols, Markdown contracts, templates, handoff assets, state-machine or algorithm specifications, and owner-local `tool` or `test`; every owner-local asset MUST remain under that owner root.
```

- `B7-4 Amendment to B4-2 Main project`
  - source: approved `B4-2 Main project`;
  - transformation: только `Membership` изменяется для удаления harness-specific entity list без потери project-local entities;
  - approved target wording:

```md
- Membership: it includes `Main project code`, `Main project AGENTS.md`, `Main project documentation`, root `test`, root `tool`, `Submodule`, every other project-local entity declared by applicable provider or project-local instructions, and other project-local assets such as `.codex/config.toml`, `log/**`, `Legacy`, data files, images, and text artifacts.
```

### Batch 8: Main project documentation

Пользователь явно утвердил этот batch 24 июля 2026 года после удаления `pattern/**` из target project structure. Оба protected projects содержат под `pattern/**` только одинаковый `script_workflow_owner.md`, migration и удаление которого уже принадлежат `B2-3 Script Workflow Owner Pattern`.

- `B8-1 Main project documentation`
  - sources: term `Main project documentation` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `dfa51d9f708ffe27dcf7adbc3398610160fc6e8da452aed8a3a5c94e39deb369`;
  - target owner: `project-standards/plugins/project-standards/skills/project-documentation-developer/references/documentation-model.md`;
  - transformation: old `doc/**` и `pattern/**` ownership заменяется утверждёнными `DESIGN.md`, `design/**` и `docs/**`; удаляемые harness-specific entities и active `.spec` task artifacts явно исключаются из documentation model; repository-wide rules переходят к provider-qualified skill;
  - approved target wording:

```md
- `Main project documentation`
  - Meaning: stable project documentation whose owners are `DESIGN.md`, `design/**`, and `docs/**`.
  - Membership: it includes stable architecture and domain design, operational catalogs, project-local operational workflows, user and reference documentation, and other long-lived human-readable guidance maintained outside instruction and active task artifacts.
  - Exclusions: it does not include `Main project AGENTS.md`, `Main project code`, `test`, `tool`, or `.spec` task artifacts.
  - Repository-wide rules owner: `project-standards:project-documentation-developer`.
```

### Batch 9: Main project code

Пользователь явно утвердил этот batch 24 июля 2026 года и потребовал использовать более полное source definition. Полное определение классифицирует допустимые code entities независимо от их текущего наличия в конкретном project: при последующем появлении backend, frontend или deployment entity `B3-2 External Standard Reference Rules` сразу требует выбрать и применить соответствующий capability skill без изменения самого definition.

- `B9-1 Main project code`
  - sources: term `Main project code` в обоих protected `AGENTS.md`;
  - source state:
    - `workflow-control-center` SHA-256 `3021ce152c867d5fda387f44d1eb295466e4f810d95312a7089ce1eae6a93afc` и содержит полное membership с product backend, frontend и deployment configuration;
    - `marketplace-tr-priority` SHA-256 `70f5b798f1c51885793cdba238ab43190ce05b26aa77ac2013a96921ddf9ce93` и не перечисляет эти три отсутствующие в нём entity categories;
  - verified current state: `workflow-control-center` содержит roots `backend/`, `deploy/` и `ui/`; `marketplace-tr-priority` не содержит этих roots;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/repository-model.md`;
  - transformation: используется полное `workflow-control-center` membership как reusable classification, а не требование физического наличия всех categories; явно исключается `Main project documentation`; удаляются упразднённые `Subagent config` и `Subagent group`; local-only rule owner заменяется утверждённой external standard composition;
  - approved target wording:

```md
- `Main project code`
  - Meaning: the main root-repository code entity for runtime and business logic.
  - Membership: it includes product backend code, product frontend code, product deployment configuration, reusable library code, persisted-entity code, script-family code, and root entrypoints when those entrypoints are runtime or business wrappers rather than standalone support utilities.
  - Exclusions: it does not include `Main project AGENTS.md`, `Main project documentation`, `Skill`, `Submodule`, `test`, `tool`, or `Legacy`, including thin root Python entrypoints whose implementation owner is `Legacy`.
  - Rule composition: it follows the shared `Code` layer, applicable capability skills selected through `External Standard Reference Rules`, and explicitly authorized project-local specializations within their declared scope.
```

### Batch 10: Code

Пользователь явно утвердил этот batch 24 июля 2026 года. `Code` становится compositional term для полного набора применимых capability rule slices и не создаёт единого consumer-local `Code Rules` owner. Добавление `test` в явный список owner-specific specializations обеспечивает применение общего code layer к test code без замены его более узкого contract.

- `B10-1 Code`
  - sources: term `Code` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `1f58a69699741228a5559c21db54dca092d6abe3716a2072a497efa97cf158bf`;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/repository-model.md`;
  - transformation: прежний single owner `Code Rules` заменяется композицией применимых capability skills через `External Standard Reference Rules`; `test` явно добавляется к owner-specific specializations; consumer-local monolithic copy запрещается;
  - approved target wording:

```md
- `Code`
  - Meaning: the shared code-rule layer composed from every applicable code capability skill selected through `External Standard Reference Rules`.
  - Scope relation: `Code` is the common rule layer beneath owner-specific specializations such as `Main project code`, `Submodule code`, `test`, and `tool`; it does not replace the local ownership of those entities.
  - Ownership: each selected capability skill remains the single owner of its contributed rule slice; `Code` does not create a second monolithic rule owner or consumer-local copy.
```

### Batch 11: test

Пользователь явно утвердил этот batch 24 июля 2026 года. Generic `test` definition сохраняет production boundary и verification ownership, но передаёт конкретное размещение применимому testing capability и project-local structure contract: Python tests могут использовать owner-local `test/**`, тогда как frontend tests могут оставаться co-located с TypeScript code.

- `B11-1 test`
  - sources: term `test` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `fbe0197c66a2e91207d3df35279c3bb47ce9ada04697387d021a57e2b65adca8`;
  - verified current state: `workflow-control-center` содержит frontend tests под `ui/src/**/*.test.ts` и `ui/src/**/*.test.tsx`, поэтому требование одного owner-local `test/` root не является reusable cross-technology placement contract;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/repository-model.md`;
  - transformation: removed `Subagent group` заменяется общим явно объявленным `Self-contained` owner; placement маршрутизируется к testing capability и project-local structure; local-only `Test Rules` owner заменяется утверждённой external standard composition;
  - approved target wording:

```md
- `test`
  - Meaning: verification code for the `Main project` or for one explicitly declared `Self-contained` owner within it, including a `Submodule` or `Skill`.
  - Role: a support entity, not a business owner.
  - Production-boundary rule: `test` must not become the only owner of production algorithms or behavior.
  - Owner-local placement and scope: `test` uses the canonical placement declared by the owning entity's applicable testing capability and project-local structure contract, and owns verification of that entity, its local support slices, and its owned integration contracts.
  - Rule composition: it follows the shared `Code` layer, applicable testing capability skills selected through `External Standard Reference Rules`, and explicitly authorized project-local specializations within their declared scope.
```

### Batch 12: tool

Пользователь явно утвердил этот batch 24 июля 2026 года. Generic `tool` definition сохраняет support-only role, English output contract и owner-local helper placement, но охватывает любой явно объявленный `Self-contained` owner и получает capability-based rule composition.

- `B12-1 tool`
  - sources: term `tool` в обоих protected `AGENTS.md`;
  - source state: term blocks byte-identical, SHA-256 `fcd02c4181c62faec891f8f9224bad73d5ab74cb90cec8d87c02715e70f41cee`;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/repository-model.md`;
  - transformation: removed `Subagent group` заменяется общим явно объявленным `Self-contained` owner; local-only `Tool Rules` owner заменяется утверждённой external standard composition; остальные source semantics сохраняются;
  - approved target wording:

```md
- `tool`
  - Meaning: support and control code for the `Main project` or for one explicitly declared `Self-contained` owner within it, including a `Submodule` or `Skill`.
  - Role: a support entity, not a business owner.
  - Output contract: `tool` output, logging, and help text must be English unless a local owner explicitly says otherwise.
  - Owner-local placement: `tool` uses the canonical local tool root declared by its owning entity; shared helper code for that tool root lives under its local `tool/lib/**` branch.
  - Rule composition: it follows the shared `Code` layer, applicable tool capability skills selected through `External Standard Reference Rules`, and explicitly authorized project-local specializations within their declared scope.
```

### Batch 13: model_sqlalchemy

Пользователь явно утвердил этот batch 24 июля 2026 года. Два конфликтующих source contracts объединяются по intrinsic persisted-entity boundary: canonical ORM model может владеть row-local invariants, alternative constructors и behavior, но не product workflow, boundary или service orchestration.

- `B13-1 model_sqlalchemy`
  - sources: term `model_sqlalchemy` в обоих protected `AGENTS.md`;
  - source state:
    - `workflow-control-center` SHA-256 `de534a61beb9f7e9cc9d7fa3dfc9a2fa277073dd7061afeac16c3b96a6ad82aa` разрешает ORM models, database registry и DB-specific DDL, но запрещает product behavior;
    - `marketplace-tr-priority` SHA-256 `3562d0a362cbda7ea0c34e558e85287ab498aa3076e3670690ef535289cdcf60` разрешает business methods канонической persisted entity;
  - verified current state: `marketplace-tr-priority/model_sqlalchemy/**` содержит row-local instance methods и alternative constructors, тогда как `workflow-control-center/model_sqlalchemy/**` не требует таких методов;
  - target owner: `project-standards/plugins/project-standards/skills/sqlalchemy-developer/references/model-sqlalchemy.md`;
  - transformation: сохраняются root ownership, portability и exclusivity; business-method allowance сужается до intrinsic row-local behavior; WCC prohibition сужается до workflow, boundary, cross-entity service, integration и non-DB ownership; добавляется provider-qualified rules owner;
  - approved target wording:

```md
- `model_sqlalchemy`
  - Meaning: the canonical persisted-entity owner root family.
  - Subset status: `model_sqlalchemy` is a specialized subset of `Main project code` for persisted-entity ownership.
  - Root owner: `model_sqlalchemy/**` in the root repository.
  - Submodule portability: inside a `Submodule`, use the same `model_sqlalchemy/**` owner root relative to the `Submodule` root.
  - Exclusivity: do not create a parallel canonical owner for an entity already owned by `model_sqlalchemy`.
  - Allowed contents: ORM table models; persisted-entity invariants, alternative constructors, and behavior intrinsic to one entity whose implementation operates only on that row's mapped attributes or mapped relationships; project database registry; and DB-specific function or view DDL definitions.
  - Forbidden contents: backend API validation, request or response schemas, UI behavior, product workflow or orchestration logic, behavior owned by a cross-entity service or external integration, and non-DB helper libraries.
  - Repository-wide rules owner: `project-standards:sqlalchemy-developer`.
```

### Batch 14: Amendment to B6-3 Submodule code

Пользователь явно утвердил этот batch 24 июля 2026 года. По той же future-proof classification, которая утверждена для `Main project code`, `Submodule code` заранее охватывает возможные backend, frontend и deployment entities внутри самостоятельного sibling repository и автоматически получает применимые capability skills при их появлении.

- `B14-1 Amendment to B6-3 Submodule code`
  - source: approved `B6-3 Submodule code`;
  - target owner: `project-standards/plugins/project-standards/skills/submodule-developer/references/submodule-model.md`;
  - transformation: membership расширяется общими product backend, frontend и deployment categories; local-only `Submodule Code Rules` owner заменяется композицией shared `Code`, capability skills owning Submodule и applicable `Submodule AGENTS.md` chain; остальные source semantics сохраняются;
  - approved target wording:

```md
- `Submodule code`
  - Meaning: the code entity owned by one `Submodule` for its local runtime and business logic.
  - Membership: it includes product backend code, product frontend code, product deployment configuration, reusable library code, persisted-entity code, script-family code, and submodule entrypoints when those entrypoints are runtime or business wrappers rather than standalone support utilities.
  - Exclusions: it does not include `Submodule AGENTS.md`, local `test`, local `tool`, or other non-code local assets of that `Submodule`.
  - Rule composition: it follows the shared `Code` layer, capability skills selected for the owning `Submodule` through `External Standard Reference Rules`, and the applicable `Submodule AGENTS.md` chain within its declared boundary.
```

### Batch 15: Standard Submodule List ownership

Пользователь явно утвердил этот batch 24 июля 2026 года. Concrete submodule purpose, public interface и reusable host integration переходят к stable design самого submodule repository; consumer `AGENTS.md` сохраняет path mapping, exact external contract reference и только собственную project-local specialization. Provider-first cutover запрещает сокращать consumer block до появления и semantic verification нового owner.

- `B15-1 Amendment to B3-1 Standard Submodule List Rules`
  - sources: subsection `Standard Submodule List Rules` в обоих protected `AGENTS.md`;
  - source state: subsections byte-identical, SHA-256 `578f47208bfec9258d274e1461f762a70c62224c1c42ffed7ead17e7aec18544`;
  - verified current state:
    - оба projects имеют по 13 configured git submodules и 12 общих concrete submodules;
    - `workflow-control-center` дополнительно имеет `base_worker`;
    - `marketplace-tr-priority` дополнительно имеет `compose_playwright`;
    - большинство concrete submodules пока не имеют собственного stable host-facing design contract;
  - target owner: `project-standards/plugins/project-standards/skills/submodule-developer/references/submodule-registry.md`;
  - transformation: consumer-local complete contract ownership заменяется reference model; stable reusable contract принадлежит `DESIGN.md` owning submodule; `Submodule AGENTS.md` сохраняет только submodule-internal scope; project-local specialization остаётся в consumer и регулируется `External Standard Reference Rules`;
  - approved target wording:

```md
### Standard Submodule List Rules

- A governed project MUST list every concrete standard `Submodule` root represented in its `Key Directory Map`.
- Each entry MUST state the canonical submodule name, root path, exact submodule-owned host contract path, and only those applicability or integration constraints that are specific to the consuming project.
- The submodule's `DESIGN.md` or a document routed by it under `design/**` is the canonical owner of that submodule's stable purpose, public interface, and reusable host-integration contract.
- `Submodule AGENTS.md` governs work inside the submodule boundary and MUST NOT own rules for host code outside that boundary.
- A consuming project MUST reference the submodule-owned contract and MUST NOT copy or paraphrase it.
- A consumer entry MAY add a project-local specialization only within that consuming project's boundary and only under `External Standard Reference Rules`.
- A consumer block MUST NOT be shortened or removed until the referenced submodule-owned contract exists and has passed semantic verification.
```

### Batch 16: model foundation submodules

Пользователь явно утвердил этот batch 24 июля 2026 года. Stable purpose, public interface и reusable host integration `base_model` и `base_model_sqlalchemy` переходят к `DESIGN.md` соответствующих submodule repositories; protected consumer entries становятся exact contract references только после provider-first implementation и semantic verification.

- `B16-1 base_model`
  - sources: block `base_model` в `Standard Submodule List` обоих protected `AGENTS.md`;
  - source state: blocks byte-identical, SHA-256 `d956c8778eeff8f9d5a8909d3aa383f7c19ac8b1116f3f3fb4c8c5f4f041843a`;
  - verified current state:
    - `workflow-control-center` и `marketplace-tr-priority` напрямую используют package-root export `BaseModelStrict`;
    - direct imports через внутренний path `base_model.strict` остаются только в нескольких consumer tests и не образуют production public API;
  - provider target owner: `base_model/DESIGN.md` в repository `submodule-base-model`;
  - applicability owner: `project-standards:python-developer`;
  - transformation: reusable purpose, runtime foundation, public interface и host integration переносятся к owning submodule design; consumer-local block заменяется exact provider reference без project-local specialization;
  - approved provider wording:

```md
# base_model

## Purpose

`base_model` owns the shared strict Pydantic foundation for validated detached objects.

## Public interface

`BaseModelStrict`, exported from the `base_model` package root, is the canonical public interface. Internal module paths are not consumer APIs.

## Runtime contract

`BaseModelStrict` uses standard Pydantic validation with `strict=True`, `validate_assignment=True`, `validate_default=True`, and `extra="forbid"`.

Copying with field overrides rebuilds the object through its normal constructor so that the overrides receive the same validation as ordinary construction.

## Host integration

Consumers subclass `BaseModelStrict` instead of copying, wrapping, or independently reimplementing its validation foundation. The applicable development standard determines which consumer objects must use this base.
```

  - approved protected consumer wording:

```md
- `base_model`
  - Root path: `base_model/`.
  - Host contract: `base_model/DESIGN.md`.
```

- `B16-2 base_model_sqlalchemy`
  - sources: block `base_model_sqlalchemy` в `Standard Submodule List` обоих protected `AGENTS.md`;
  - source state: blocks byte-identical, SHA-256 `b4ccc8f1ede59919d2b4053db0c89d6c5695aa9e83b29da84edd411094554296`;
  - verified current state:
    - оба protected projects напрямую используют package-root API `base_model_sqlalchemy`;
    - production consumer code импортирует foundation objects через package root;
    - direct imports из внутренних modules остаются только в отдельных consumer tests;
    - `workflow-control-center` закрепляет revision `35d4a50cd987e377b2579e49c4128bbb91c4c622`, а `marketplace-tr-priority` — descendant revision `b2d8936ddcd6b747ab724699493cb8a5bb1cf801`;
  - provider target owner: `base_model_sqlalchemy/DESIGN.md` в repository `submodule-base-model-sqlalchemy`;
  - applicability owner: `project-standards:sqlalchemy-developer`;
  - version-convergence contract: canonical provider contract создаётся на актуальной основной ветке submodule; каждый consumer gitlink обновляется только после его собственных compatibility tests; устаревшая pinned revision не становится отдельным stable contract;
  - transformation: reusable purpose, ownership boundary, public interface и host integration переносятся к owning submodule design; consumer-local block заменяется exact provider reference без project-local specialization;
  - approved provider wording:

```md
# base_model_sqlalchemy

## Purpose

`base_model_sqlalchemy` owns reusable SQLAlchemy foundations below concrete persisted-entity, database-registry, and database-support-object owners.

## Public interface

The names exported from the `base_model_sqlalchemy` package root through `__all__` form the canonical public interface. Internal module paths are not consumer APIs unless this design explicitly declares an additional stable surface.

## Ownership boundary

This submodule owns reusable ORM base behavior, database specification mechanics, schema validation, model-package registration, and database-specific support-object foundations.

Concrete persisted entities, project database identities, product workflows, and integration-specific persistence behavior remain owned by the consuming project or consuming submodule.

## Host integration

Consumers build their concrete persisted-entity and database contracts on the package-root public interface instead of cloning the shared foundation. The applicable SQLAlchemy development standard determines when each public abstraction is required.
```

  - approved protected consumer wording:

```md
- `base_model_sqlalchemy`
  - Root path: `base_model_sqlalchemy/`.
  - Host contract: `base_model_sqlalchemy/DESIGN.md`.
```

### Batch 17: API capability split, temporal data и base_api_schema

Пользователь явно утвердил этот batch 24 июля 2026 года с дополнительным требованием не считать все текущие результаты timezone normalization ошибочными без проверки полного source-to-storage path. Final migration доказывает сохранение exact instant для каждого external source contract и не сдвигает повторно уже сохранённые naive UTC значения.

- `B17-1 API capability split`
  - source: ранее утверждённый initial capability `rest-api-developer`;
  - target owners:
    - `project-standards:rest-api-server-developer` для inbound HTTP routes, OpenAPI, authorization, access control и server boundary;
    - `project-standards:http-api-client-developer` для outbound HTTP integrations, transport wrappers, retry boundaries и external API schemas;
  - transformation: один неоднозначный capability разделяется на две независимо применимые boundary capabilities; project с обеими boundaries обязан выбрать обе;
  - protected consumer impact: `Required Standards` выбирает capabilities по real current boundaries через `External Standard Reference Rules`, не копируя их contracts.

- `B17-2 Temporal Data Contract`
  - source: явное новое cross-project требование пользователя;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/temporal-data.md`;
  - dependent capabilities: `python-developer`, `sqlalchemy-developer`, `rest-api-server-developer`, `http-api-client-developer`, `typescript-developer` и `react-ui-developer` применяют этот contract через exact reference и не копируют его;
  - transformation: новый reusable cross-technology contract;
  - approved target wording:

```md
# Temporal Data Contract

- Every datetime that represents an instant uses UTC as its canonical internal timezone.
- Runtime instant values must be timezone-aware and normalized to UTC. Removing `tzinfo` from an instant is forbidden.
- At an ingress boundary:
  - an offset-aware value is converted to UTC;
  - an offset-less value may be accepted only when the external contract explicitly declares it to be UTC, after which UTC timezone information is attached;
  - otherwise the boundary must reject the value or convert it from an explicitly declared source timezone.
- Persistence, internal messages, caches, and background jobs preserve the exact UTC instant and required precision.
- A storage protocol that cannot represent timezone-aware values may use naive UTC only inside its adapter boundary. The adapter must enforce UTC and restore timezone-aware UTC immediately after reading.
- APIs serialize instants as RFC 3339 UTC values with `Z` or `+00:00`.
- Conversion to a user or account timezone happens only at the presentation boundary, such as the frontend or a human-oriented export. A presentation value must not replace the canonical UTC value.
- A calendar date, local time of day, or timezone-dependent business schedule is not an instant and must be modeled separately. When such a value depends on a timezone, it stores an explicit IANA timezone identifier.
```

- `B17-3 base_api_schema`
  - sources: block `base_api_schema` в `Standard Submodule List` обоих protected `AGENTS.md`;
  - source state: blocks byte-identical, SHA-256 `816b94884f22dc83c83960e57b9b633a1014f02b8205a02970ee203341f6161b`;
  - verified current state:
    - `workflow-control-center` и `marketplace-tr-priority` закрепляют revision `6c786d730e58d9f63fc1a18343a2e85362e7d6a4`, тогда как older `marketplace-tools` и `marketplace-tr` consumers закрепляют ancestor revision `767c29b69ca7945d4ffe57fd2358c15e1e1b4862`;
    - ancestor revision содержит `@model_serializer(mode="wrap")`, который исключает undeclared external fields из serialization; current main потерял decorator и сериализует эти fields;
    - `BaseApiSchema.__hash__()` возвращает SHA-256 string, поэтому Python `hash(instance)` завершается `TypeError`; fresh terminal audit обнаружил прямые вызовы этого метода в ClickHouse persistence consumers, где SHA-256 использовался как row fingerprint, а не как Python hash protocol;
    - current `remove_timezone()` удаляет `tzinfo` без собственного `astimezone(UTC)`;
    - это не доказывает, что все текущие resulting instants неверны: `Ozon` test fixture использует `Z`, поэтому удаление UTC marker не сдвигает wall-clock value, а часть `Wildberries` schemas для documented Moscow `UTC+3` naive timestamps сначала вычитает три часа в concrete field validator и получает правильный naive UTC instant;
    - current `Wildberries` behavior уже учитывает риск double conversion при чтении persisted UTC rows, поэтому existing naive UTC storage MUST NOT повторно интерпретироваться как Moscow source time;
  - provider target owner: `base_api_schema/DESIGN.md` в repository `submodule-base-api-schema`;
  - applicability owner: `project-standards:http-api-client-developer`;
  - transformation:
    - reusable purpose, public interface, forward-compatible response parsing и host integration переходят к owning submodule design;
    - broken `__hash__` удаляется из external API schema owner; обнаруженный real row-fingerprint contract переносится в ClickHouse persistence owner под явным именем `fingerprint_get()`, и все consumer call sites мигрируют на него;
    - serializer contract восстанавливается и покрывается provider tests;
    - generic timezone-marker deletion удаляется; concrete integration boundary нормализует source timestamp по exact source contract в canonical aware UTC;
    - consumer-local block заменяется exact provider reference без project-local specialization;
  - approved provider wording:

```md
# base_api_schema

## Purpose

`base_api_schema` owns the shared Pydantic foundation for forward-compatible external HTTP API response schemas.

## Public interface

`BaseApiSchema`, exported from the `base_api_schema` package root, is the canonical public interface. Internal module paths are not consumer APIs.

## Runtime contract

`BaseApiSchema` accepts undeclared response fields so that additive external API changes do not break response parsing.

Serialization emits only fields declared by the concrete schema and serializes declared enum values through their external values.

External datetime values follow the shared `Temporal Data Contract`. The base class never discards a timezone offset. Source-specific interpretation of offset-less values belongs to the concrete external API integration.

The base class does not define object hashing.

## Ownership boundary

This submodule owns only behavior common to external HTTP API response schemas.

Internal domain objects, configuration objects, persistence objects, API server request schemas, and API server response schemas use their own applicable contracts instead of `BaseApiSchema`.

## Host integration

Concrete external API integrations define an integration-local schema base that subclasses `BaseApiSchema`. The applicable HTTP API client standard determines when this foundation is required.
```

  - approved protected consumer wording:

```md
- `base_api_schema`
  - Root path: `base_api_schema/`.
  - Host contract: `base_api_schema/DESIGN.md`.
```

  - implementation and compatibility acceptance:
    - inventory every concrete consumer datetime field and record whether its external source is offset-aware UTC, offset-aware non-UTC, contract-declared naive UTC, contract-declared naive named-zone time, or invalidly ambiguous;
    - use official external contract evidence where available and retain representative raw fixtures for each accepted source form;
    - verify exact instant equality before and after normalization for `Z`, non-zero offsets, declared naive UTC and declared naive named-zone inputs;
    - reject ambiguous offset-less values instead of guessing;
    - use an explicit IANA timezone for named-zone sources instead of arithmetic offset subtraction;
    - treat existing persisted naive UTC values as UTC at the storage adapter boundary and attach UTC without shifting them;
    - prevent persisted rows from re-entering external-source normalization;
    - test that undeclared input fields remain accepted but are absent from serialization, enums use external values, canonical datetime output is aware UTC, and instances remain unhashable;
    - update each consumer gitlink only after its own end-to-end compatibility tests prove unchanged instants and expected storage values.

### Batch 18: config_env

Пользователь явно утвердил этот batch 24 июля 2026 года. `config_env` получает stable provider-owned contract с explicit project root, process-environment precedence, preserved empty strings и fail-fast typed readers. Final public API заменяет обе исторические call shapes coordinated migration без compatibility aliases.

- `B18-1 runtime-config-developer`
  - source: явная capability gap, выявленная при разборе `config_env`;
  - target owner: `project-standards:runtime-config-developer`;
  - applicability: capability обязателен для project с runtime configuration boundary, environment readers, dotenv loading или validated runtime config objects;
  - relationship:
    - `project-foundation` сохраняет общую repository model;
    - `runtime-config-developer` владеет reusable configuration precedence, validation, secret-safety и bootstrap rules;
    - language, CLI, deployment и integration capabilities ссылаются на него и не копируют эти rules;
  - transformation: новый independently applicable capability.

- `B18-2 config_env`
  - sources: block `config_env` в `Standard Submodule List` обоих protected `AGENTS.md`;
  - source state: blocks byte-identical, SHA-256 `e7ef01e1ec42a9999e1eadf3b7a71d43aa3e8ea19b6ea14bc5693289ab71c9e0`;
  - verified current state:
    - protected projects используют revision `618036bd88f05b657ecd035da48011f17c9af957` и current package-root names `env_config_load`, `env_int_get`, `env_value_get`, `is_env_key_enabled`;
    - older consumers используют revision `8ef2be044c44a1a078728eaac47e55228ba80784` и package-root name `config_env`;
    - current `env_value_get` считает present empty string отсутствующим;
    - current integer reader silently returns default for malformed present values;
    - current boolean reader silently maps every unrecognized present value to `False`;
    - current dotenv loader uses ancestor-searching `find_dotenv` and loads `.env_devel` with `override=True`, allowing a local file to replace explicit process environment;
  - provider target owner: `config_env/DESIGN.md` в repository `submodule-config-env`;
  - applicability owner: `project-standards:runtime-config-developer`;
  - transformation:
    - reusable purpose, public interface, precedence, typed reading, security boundary и host integration переходят к owning submodule design;
    - `env_config_load` получает mandatory explicit `project_root: Path`;
    - `is_env_key_enabled` заменяется consistent package-root name `env_bool_get`;
    - present empty string сохраняется как `''`;
    - present malformed typed values fail instead of silently selecting a fallback;
    - отдельно утверждённые non-`Legacy` consumers мигрируют old `config_env` and current `is_env_key_enabled` call sites напрямую на final API без compatibility aliases;
    - `Legacy` consumers сохраняют существующие imports, calls, dotenv semantics и provider revisions согласно task-specific исключению в `Non-goals`;
    - consumer-local block заменяется exact provider reference без project-local specialization;
  - approved provider wording:

```md
# config_env

## Purpose

`config_env` owns shared process-environment reading and explicit project-root dotenv loading mechanics.

## Public interface

The canonical package-root public interface consists of `env_bool_get`, `env_config_load`, `env_int_get`, and `env_value_get`. Internal module paths are not consumer APIs.

## Dotenv loading contract

`env_config_load` receives the owning project root explicitly and reads only `.env`, `.env_devel`, and `.env_secret` directly below that root.

Configuration precedence from strongest to weakest is:

1. process environment;
2. `.env_secret`;
3. `.env_devel`;
4. `.env`.

A missing dotenv file is allowed. Dotenv loading never searches parent directories and never replaces a value already present in the process environment.

`.env_secret` and `.env_devel` are local ignored files. A tracked `.env` may contain only non-secret defaults.

## Reader contract

`env_value_get` preserves the exact present string, including `''`, and uses its default only when the key is absent.

`env_int_get` and `env_bool_get` use their typed defaults only when the key is absent. A present value that cannot be parsed into the required type raises a configuration error.

Boolean parsing accepts explicit case-insensitive true values `1`, `true`, `yes`, `y`, and `on`, and false values `0`, `false`, `no`, `n`, and `off`. Other present values are invalid.

## Security boundary

Environment values and dotenv contents are never included in ordinary logs or exception messages. Diagnostics may identify the invalid or missing key.

## Host integration

One root bootstrap calls `env_config_load` exactly once before constructing runtime configuration objects or starting concurrent work.

Importing `config_env` has no environment mutation side effects. Project configuration models own required fields, domain validation, cross-field invariants, and derived values.
```

  - approved protected consumer wording:

```md
- `config_env`
  - Root path: `config_env/`.
  - Host contract: `config_env/DESIGN.md`.
```

  - implementation and compatibility acceptance:
    - verify exact precedence for every pair of process environment, `.env_secret`, `.env_devel` and `.env`;
    - verify missing files and explicit project-root confinement;
    - verify present `''` remains distinct from an absent key;
    - verify accepted true and false tokens and failure for every other present boolean token;
    - verify valid integer parsing and failure for malformed present integer values;
    - verify diagnostics name only the key and never expose its value;
    - migrate only separately approved non-`Legacy` consumer call sites in the coordinated cutover;
    - verify changed-file inventories of `Legacy` consumers contain no dotenv, `load_dotenv()`, `config_env`, `config_logging` or `config_sqlalchemy` bootstrap or gitlink convergence changes;
    - update a migrated consumer gitlink only after its own bootstrap and configuration tests pass.

### Batch 19: config_argparse

Пользователь явно утвердил этот batch 24 июля 2026 года. `config_argparse` сохраняет default-on environment binding и отображение effective deployment defaults для обычных значений. `secret=True` объявляет environment-only configuration: соответствующий CLI option не существует, а secret value никогда не попадает в `argv` или help.

- `B19-1 config_argparse`
  - sources: block `config_argparse` в `Standard Submodule List` обоих protected `AGENTS.md`;
  - source state: blocks byte-identical, SHA-256 `8ccce94d2fb05333ad87968b7c4a60e7b2c3e3a92f67fecd82ce25399049a2c9`;
  - verified current state:
    - `workflow-control-center` использует revision `085769ceb05829dc46e5af9ce6e57236473ecd62`;
    - `marketplace-tr-priority` использует более новую revision `609e1f4995a45583e2ed1673ed43275a43a326ce`, где единственный внешний consumer потребовал public `ConfigArgumentParser.env_key_get`;
    - package root экспортирует `ConfigArgumentParser`, `env_key_build` и `script_prefix_get`, хотя два free-function helpers не имеют внешних consumers;
    - environment binding автоматически применяется к подходящим optional long flags, а CLI-only arguments явно используют `env=False`;
    - environment key строится CamelCase-конкатенацией parser prefix и long flag, включая parser-level и argument-level `env_prefix`;
    - help показывает environment key и фактический environment-backed default;
    - malformed present integer, boolean и arbitrary typed values могут silently выбирать declared default, а `choices` не образуют единый environment validation boundary;
    - `platform_token`, `control_master_secret` и `bearer_token` объявлены как обычные CLI options, хотя production-like runtime wiring уже передаёт основные platform secrets через environment;
    - имена `storage_secret_bucket` и `storage_secret_kms_key_id` содержат token `secret`, но сами значения не являются credentials, поэтому name-based secret inference некорректен;
  - provider target owner: `config_argparse/DESIGN.md` в repository `submodule-config-argparse`;
  - applicability owners:
    - `project-standards:python-cli-developer` владеет reusable CLI shape и parsing rules;
    - `project-standards:runtime-config-developer` владеет environment precedence, validation и secret-safety rules;
  - transformation:
    - default-on environment binding сохраняется;
    - effective deployment defaults для обычных environment-backed values сохраняются в help;
    - `secret=True` получает environment-only semantics вместо masking одного остающегося CLI option;
    - `ConfigArgumentParser.env_key_get` сохраняется как real public method для shared и aggregate CLI owners;
    - `env_key_build` и `script_prefix_get` становятся internal implementation details;
    - unused `env_key` и `include_env_in_help` extension knobs удаляются;
    - malformed used environment values fail without silent fallback;
    - existing generated environment names не меняются;
    - consumer-local block заменяется exact provider reference без project-local specialization;
  - approved provider wording:

```md
# config_argparse

## Purpose

`config_argparse` owns one shared `argparse`-compatible parser with generated environment-backed defaults, deployment-aware help, and environment-only secret configuration.

## Public interface

The canonical package-root public interface consists only of `ConfigArgumentParser`. Its parser-specific public surface consists of constructor-level `env_prefix`, the `env`, `env_prefix`, and `secret` `add_argument` metadata, and `env_key_get`.

Environment-key builders, script-prefix builders, formatter classes, and internal parsing helpers are not consumer APIs.

## Environment key contract

The parser resolves its environment prefix once from explicit `env_prefix` or, when omitted, from the executable filename stem. It converts the prefix and the first declared long option to CamelCase and concatenates them. An argument-level `env_prefix` replaces the parser prefix for that argument.

Environment binding is enabled by default for each non-required optional argument that has a long option and is not the help action. A CLI-only argument declares `env=False`.

Generated environment names are external configuration interfaces. Renaming an executable, prefix, or bound long option requires a coordinated environment-contract migration.

## Value precedence and validation

For an ordinary environment-backed argument, precedence from strongest to weakest is explicit CLI input, the generated environment key, and the declared parser default.

An environment value is parsed only when explicit CLI input does not replace it. A used environment value follows the same declared type and choices constraints as CLI input. Invalid used values fail parsing and identify the environment key without including its value. Silent fallback to the declared default is forbidden.

A present empty string remains the effective value for a string argument. Typed readers reject an empty string when it is not valid for the declared type.

Standard scalar and boolean actions use parser-owned environment conversion. A shared or aggregate action whose default is a validated object uses `env_key_get` to obtain the same generated key and keeps its environment conversion and validation in that object's owner. Raw unvalidated fallback is forbidden.

## Secret contract

`secret=True` declares one raw credential, token, private key, password, or equivalent secret value. Secret classification is explicit and never inferred from an argument name.

The long option in a `secret=True` declaration defines only the destination field and generated environment key. The parser does not register that option as CLI input, does not accept it in `argv`, and does not show it in the CLI option list.

A secret value is read only from its generated environment key and is placed in the parsed namespace for validated configuration construction. `secret=True` is incompatible with `env=False`. A literal non-empty secret default is forbidden; the canonical absent default is `''`.

Help lists secret declarations in a separate environment section by environment key and description without rendering their values. Secret values are absent from parser errors and ordinary logs.

## Help contract

Help sorts options alphabetically, suppresses optional metavar echo, and names the generated environment key for every environment-backed ordinary option.

For an ordinary option, help renders the effective deployment default, including a value supplied through the environment. For a secret declaration, help never renders the value.

Help remains available even when a configured environment value would fail runtime parsing.

## Host integration

The root bootstrap finishes its project-owned environment loading before constructing `ConfigArgumentParser`.
The parser reads only the resulting process environment, does not load dotenv files, and does not depend on one
host `config_env` revision.

Parsed values are handed directly to the owning validated configuration model. That model owns required-value rules, cross-field invariants, and derived values.
```

  - approved protected consumer wording:

```md
- `config_argparse`
  - Root path: `config_argparse/`.
  - Host contract: `config_argparse/DESIGN.md`.
```

  - implementation and compatibility acceptance:
    - verify unchanged CamelCase environment-key generation for explicit parser prefix, executable-derived prefix and argument-level prefix;
    - verify environment binding remains default-on and `env=False` leaves one argument CLI-only;
    - verify precedence for CLI, environment and declared default, including a valid CLI override of one malformed lower-priority environment value;
    - verify identical type and choices validation for used CLI and environment inputs without silent fallback;
    - verify a present empty string remains distinct from an absent string value;
    - verify help renders actual effective non-secret environment defaults and never substitutes only the source-code default;
    - verify `secret=True` places an environment value into the target namespace, omits the pseudo-option from the CLI option list and rejects that pseudo-option in `argv`;
    - verify secret help lists the environment key and description without the value, and parser errors and logs do not expose the value;
    - reject `secret=True` with `env=False` and reject non-empty literal secret defaults;
    - retain direct behavior coverage for the real aggregate `env_key_get` consumer;
  - migrate `platform_token`, `control_master_secret` and `bearer_token` declarations and their tests to environment-only input;
  - keep `storage_secret_bucket` and `storage_secret_kms_key_id` as ordinary non-secret configuration;
  - update validation messages that still describe removed secret CLI options;
  - keep `config_argparse` independent from one `config_env` revision: the host loads its environment first and
    the parser reads only the resulting process environment;
  - update each consumer gitlink only after its direct CLI, help, bootstrap and configuration tests pass.

### Batch 20: config_logging

Пользователь явно утвердил этот batch 24 июля 2026 года. `config_logging` остаётся единственным владельцем bootstrap стандартного Python logging, но перестаёт владеть временными каталогами, Playwright-specific paths, отправкой логов и S3 upload state.

- `B20-1 config_logging`
  - sources: block `config_logging` в `Standard Submodule List` обоих protected `AGENTS.md`;
  - source state: blocks byte-identical, SHA-256 `d1decd91f181ec7b2605f030021ad6a6fa95e818245c262d202a5c89dad8f610`;
  - verified consumer state:
    - шесть consumers используют четыре distinct revisions:
      - `marketplace-next-co-uk` использует `26cd6e77`;
      - `marketplace-parser` использует `c78d2652`;
      - `marketplace-tools` на ветке `marketplace_tools` использует `20bcf152`;
      - `marketplace-tr-priority` использует `d928d53e`;
      - `marketplace-tr` использует `c78d2652`;
      - `workflow-control-center` использует `d928d53e`;
    - canonical current `main` revision — `d928d53eb2250d0154b86e872dfa39e924750804`;
    - единственное дополнительное исправление ветки `marketplace-tools` после `c78d2652` — исправление `return` внутри `finally`, уже присутствующее в canonical `main`;
    - current `main` содержит актуальную поддержку git worktree refs, Python 3.14 и текущие имена;
    - package root экспортирует `LOG_RECORD_SEPARATOR`, `git_version_get`, `is_filename_log`, `log_dir_get`, `logging_configure`, `logging_level_int_parse`, Playwright-specific helpers, script metadata helpers и `tmp_dir_get`;
    - `logging_configure` сохраняет устаревшие параметры `force`, direct console/file levels, `suffix` и `custom_log_file_path`;
    - record timestamp форматируется из local timezone без явного UTC, хотя filename timestamps уже используют UTC;
    - Playwright log directory создаётся безусловно при общем logging bootstrap;
    - старые email, shared-memory и S3 mechanics уже удалены из current `main` и не являются частью целевого logging provider;
    - прежняя веточная настройка `MARKETPLACE_LOG_TIMESTAMP` является project-specific и отсутствует в current `main`;
  - provider target owner: `config_logging/DESIGN.md` в repository `submodule-config-logging`;
  - applicability owner: `project-standards:python-logging-developer`;
  - transformation:
    - существующие protected usage rules переносятся семантически без ослабления;
    - configuration levels читаются после `config_env.env_config_load` из `LoggingLevelConsole` и `LoggingLevelFile`;
    - `logging_configure` получает один canonical path override и удаляет compatibility-only параметры;
    - log record timestamp становится RFC 3339 UTC с microsecond precision, а format version меняется с `2` на `3`;
    - чтение существующего format version `2` сохраняется там, где оно требуется ingestion consumers;
    - `tmp_dir_get` переходит в `utils`;
    - Playwright-specific log helpers переходят к фактическому Playwright owner;
    - email delivery и S3 upload state остаются вне `config_logging`;
    - consumer-local block заменяется exact provider reference без project-local specialization;
  - approved protected capability wording:

```md
# python-logging-developer

Use when a project configures, writes, parses, rotates, or transports Python logging records, or when one changed runtime adds a Python logging bootstrap.

The provider `config_logging/DESIGN.md` owns the logging bootstrap and log-record interface. A project that contains Python logging MUST use that provider contract unless the user explicitly requires a project-local exception.

Logging is initialized exactly once per process at root bootstrap through `config_logging`. A second configuration attempt in the same process is an error. A spawned child process is a distinct process and may perform its own single bootstrap.

After bootstrap, product code uses direct `import logging` module calls. Product code does not create, configure, inject, cache, pass, or store logger objects.

Outside `config_logging`, `logging.getLogger(...)`, `logging.basicConfig(...)`, `logging.config.*`, logger-purpose constructor or method parameters, instance logger fields, and module-level logger variables are forbidden.
```

  - approved provider wording:

```md
# config_logging

## Purpose

`config_logging` owns one process-local bootstrap for standard Python logging, canonical console and file handlers, the shared log-record format, log filename recognition, and runtime script and git metadata used by that format.

It does not own temporary-directory policy, browser-specific artifact paths, email delivery, remote upload orchestration, or upload state.

## Public interface

The canonical package-root public interface consists only of:

- `LOG_RECORD_SEPARATOR`;
- `git_version_get`;
- `is_log_filename`;
- `log_dir_get`;
- `logging_configure`;
- `logging_level_int_parse`;
- `script_name_get`;
- `script_path_get`.

Compatibility aliases and alternate public import paths are forbidden.

## Bootstrap contract

`logging_configure(project_root: Path, *, log_file_path: Path | None = None, postfix: str = "")` initializes logging exactly once in the current process. A second call in the same process raises an error. A spawned child process may initialize logging once for its own process.

The caller invokes `config_env.env_config_load` before `logging_configure`. Console and file levels are read from `LoggingLevelConsole` and `LoggingLevelFile`.

Accepted case-insensitive level values are `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`, and `OFF`. The default is `INFO`. A malformed present value is a configuration error. `OFF` disables the corresponding handler; `NOTSET` is not an alias for disabled logging.

The default log root is `{project_root}/log`. An explicit relative `log_file_path` is resolved against `project_root`; an absolute path remains absolute. `postfix` preserves the canonical filename qualifier contract.

Parameters that independently override handler levels, force reconfiguration, provide a second filename suffix, or duplicate the canonical path override are not part of the interface.

## Product-code integration

The root bootstrap is the only configuration owner. After bootstrap, product code uses direct `import logging` module calls.

Product code does not call `logging.getLogger`, `logging.basicConfig`, or `logging.config`, and does not inject, cache, pass, or store logger objects.

## Record and file contract

Log records use RFC 3339 UTC timestamps with microsecond precision. The canonical record format version is `3`.

Consumers that parse persisted logs continue to recognize format version `2` during the coordinated compatibility window. New writers emit only version `3`.

File rotation uses UTC. Canonical log filenames and `LOG_RECORD_SEPARATOR` remain compatible with the current S3 log-ingestion boundary.

`is_log_filename` is the canonical log-filename predicate. `log_dir_get` returns the canonical project log directory without creating unrelated artifact directories.

## Ownership boundary

Temporary-directory helpers belong to `utils`.

Playwright log directory and filename-template helpers belong to the owner that integrates Playwright.

Email delivery, S3 upload orchestration, upload cursors, and upload state belong to their transport or ingestion owners and must not be added to `config_logging`.

Project-specific timestamp switches such as `MARKETPLACE_LOG_TIMESTAMP` do not belong to this provider.
```

  - approved protected consumer wording:

```md
- `config_logging`
  - Root path: `config_logging/`.
  - Host contract: `config_logging/DESIGN.md`.
```

  - implementation and compatibility acceptance:
    - verify the package-root public API contains exactly the approved names;
    - verify one successful bootstrap per PID, same-process second-call failure and independent child-process bootstrap;
    - verify strict parsing of every accepted level, default `INFO`, explicit `OFF` handler disablement and rejection of `NOTSET` and malformed present values;
    - verify default, relative and absolute log-file path behavior;
    - verify format version `3` records use RFC 3339 UTC timestamps with microsecond precision;
    - verify existing readers and S3 ingestion accept format version `2` while new writers emit only version `3`;
    - verify canonical filenames, postfix semantics and `LOG_RECORD_SEPARATOR` remain compatible with current ingestion;
    - move `tmp_dir_get` to `utils` and move Playwright-specific helpers to their actual owner without compatibility aliases;
    - verify removal of `force`, direct handler-level parameters, `suffix`, `custom_log_file_path`, `MARKETPLACE_LOG_TIMESTAMP`, email mechanics and S3 upload state from the provider surface;
    - migrate only separately approved non-`Legacy` consumer call sites and configuration bootstraps in the coordinated cutover;
    - preserve `Legacy` consumer call sites, bootstraps and gitlinks when migration would change their existing environment-loading semantics;
    - update a migrated consumer gitlink only after its direct logging, persisted-record and ingestion compatibility tests pass.

### Batch 21: utils

Пользователь явно утвердил этот batch 24 июля 2026 года. `utils` сохраняется как узкий provider generic cross-project helpers с реальными consumers, но перестаёт быть владельцем marketplace-specific S3 behavior, скрытой runtime configuration и неиспользуемых compatibility helpers.

- `B21-1 utils`
  - sources: block `utils` в `Standard Submodule List` обоих protected `AGENTS.md`;
  - source state: blocks byte-identical, SHA-256 `4cc9bd91a56fdb4c266510cff9abc2985dc5b5ef19786dea92384758fc6e26c8`;
  - verified consumer state:
    - шесть consumers используют шесть distinct revisions одного repository `submodule-utils`:
      - `marketplace-next-co-uk` использует `050dc6581d34bfc3f940e233c34c6cbbe81a658d`;
      - `marketplace-parser` использует `b849f2ebd409772b9313b4301032374129391448`;
      - `marketplace-tools` использует branch revision `41bd8009cbe8445e289b8b841ab00586f0d1bf0f`;
      - `marketplace-tr-priority` использует `0a1555df49f6b6dc67dd79e353fc26782f4e6d93`;
      - `marketplace-tr` использует `c291ba375a080cb4fc92ab0c96ae9072693d19fd`;
      - `workflow-control-center` использует canonical `origin/main` revision `4d59f6b76953b15e4fc416b922bf12e41aeea0e6`;
    - canonical `origin/main` сначала удалил marketplace-specific offer-image helpers commit `287f82a`, а затем полностью восстановил их revert commit `4d59f6b`;
    - branch `marketplace_tools` добавляет поверх canonical `main` ещё два consumer-specific S3 image commits;
    - current `utils.s3` зависит от `ConfigBoto3`, environment, `config_logging`, Requests и offer-image naming, поэтому не соответствует собственному generic boundary;
    - current `utils.string` импортирует BeautifulSoup на module import path даже для consumers, которым нужны только hashing или whitespace normalization;
    - current `pydantic_model_with_default_map_build` не имеет external consumers и synthesizes отсутствующие required values из annotations;
    - current `json_hash` functions, `string_normalize`, `datetime_version_format` и `version_datetime_parse` не имеют consumers под current names; исторический `version_from_datetime` имеет один consumer;
    - LLM translation normalization имеет четыре test consumers в `marketplace-parser` и `marketplace-tr`, но не является production generic utility;
    - реальные current modern consumers используют `atomic_file_replace`, `sha256_hash`, `slug_text_build`, `space_normalize`, `space_normalize_and_lower` и `utc_now_get`;
    - older consumers подтверждают real cross-project use case для case conversion, atomic path replacement, HTML normalization, image compression, conditional S3 access и invisible-symbol normalization;
  - provider target owner: `utils/DESIGN.md` в repository `submodule-utils`;
  - applicability owners:
    - `project-standards:python-developer` владеет общими helper necessity, reuse и code-placement rules;
    - `project-standards:submodule-developer` владеет submodule consumption и provider-boundary rules;
    - отдельный `python-utils-developer` capability не создаётся;
  - transformation:
    - protected generic reuse, minimality, domain exclusion и test/call-site synchronization semantics сохраняются;
    - package root не становится aggregate re-export surface; canonical imports идут из defining modules;
    - current modules разделяются по фактическим dependencies, чтобы обычные string consumers не импортировали HTML parser;
    - `tmp_dir_get` переходит из `config_logging` в `utils.fs` с explicit namespace и без чтения process-global script identity;
    - `atomic_dir_replace` заменяется точным именем `atomic_path_replace`, поскольку underlying operation заменяет любую filesystem entry;
    - S3 helpers используют injected client и explicit bucket, key и target path вместо environment или `ConfigBoto3`;
    - marketplace-specific offer-image behavior исключается из canonical provider;
    - неиспользуемые `pydantic`, `json_hash`, generic string normalization и datetime-version helpers удаляются из canonical provider без compatibility aliases;
    - LLM test normalization исключается из canonical `utils`; исторические pinned `Legacy` revisions сохраняют существующий helper без модернизации application tests в этой goal;
    - consumer-local block заменяется exact provider reference без project-local specialization;
  - approved provider wording:

```md
# utils

## Purpose

`utils` owns small reusable helpers whose semantics are generic, stable, and shared by real consumers across project boundaries.

`utils` is not a default placement for code that lacks another owner. Project-specific domain behavior, application configuration, environment access, business naming, and orchestration remain with their actual owners.

Before adding a shared helper elsewhere, a consumer searches the applicable `utils` public interface and reuses matching behavior. A new `utils` helper must remain minimal and generic. Contract changes update provider tests and every affected call site.

## Public interface

Consumers import public names from their defining modules. The package root does not re-export module interfaces and does not eagerly import optional module dependencies.

The canonical public interface is:

- `utils.case_convert`:
  - `camel_to_snake_convert`;
  - `json_key_camel_to_snake_convert`;
- `utils.datetime`:
  - `utc_now_get`;
- `utils.fs`:
  - `atomic_file_replace`;
  - `atomic_path_replace`;
  - `tmp_dir_get`;
- `utils.html`:
  - `html_text_normalize`;
- `utils.image`:
  - `image_compress`;
- `utils.s3`:
  - `s3_object_download`;
  - `s3_object_last_modified_get`;
- `utils.string`:
  - `invisible_symbol_remove`;
  - `sha256_hash`;
  - `slug_text_build`;
  - `space_normalize`;
  - `space_normalize_and_lower`.

Other names and internal module paths are not consumer APIs.

## Case conversion

`camel_to_snake_convert` converts one camelCase or PascalCase name to snake_case.

`json_key_camel_to_snake_convert` recursively converts mapping keys in JSON-compatible mapping and list structures without mutating the input. Scalar values and collection order are preserved.

## Temporal contract

`utc_now_get(*, microsecond: int | None = None)` returns a timezone-aware UTC instant. When `microsecond` is provided, it is applied explicitly through the normal `datetime` range contract; otherwise current microsecond precision is preserved.

Application-specific integer timestamp, version, run-id, and presentation encodings do not belong to `utils`.

## Filesystem contract

`atomic_file_replace` receives one `Path` target and one bytes payload. It writes a temporary file in the target directory, synchronizes file content, atomically replaces the target on the same filesystem, synchronizes the containing directory, and cleans up an uncommitted temporary file after failure.

`atomic_path_replace` receives explicit source and target `Path` values, performs one same-filesystem atomic replacement, and synchronizes every affected parent directory. It does not claim cross-filesystem atomicity.

`tmp_dir_get(namespace: str, relative_path: Path | None = None)` returns an ensured owner-only directory below the standard system temporary root. `namespace` is explicit and identifies the caller-owned isolation scope. Absolute paths, parent traversal, empty namespaces, symlink escapes, and hidden `sys.argv` or logging-derived namespaces are forbidden.

The caller chooses a stable namespace when shared per-script behavior is required and includes a run identifier when concurrent runs require isolation.

## HTML and image contract

`html_text_normalize` owns reusable HTML-to-plain-text normalization. Its HTML parser dependency remains isolated in `utils.html`.

`image_compress` receives source and target `Path` values and an explicit quality, writes the target image through the image-processing dependency, closes the source resource, and returns the target `Path`.

## S3 contract

S3 helpers receive an injected client plus explicit bucket, object key, and target data. They do not read environment variables, construct application-specific clients, import project configuration, or derive business object keys.

`s3_object_last_modified_get` returns an aware UTC last-modified instant when the object exists and satisfies the optional modification condition. Missing or not-modified results follow the declared return contract; authentication, authorization, transport, throttling, and other service failures propagate instead of being converted to an absent object.

`s3_object_download` downloads the object selected by one request to an explicit target `Path`, preserves conditional-request semantics, and publishes the completed local file atomically. It returns whether new content was downloaded. It does not perform a separate unbound probe followed by a second race-prone download.

## String contract

`invisible_symbol_remove` removes the explicitly supported invisible Unicode separators while preserving ordinary visible text.

`sha256_hash` returns the lowercase hexadecimal SHA-256 digest of UTF-8 text.

`slug_text_build` produces the existing deterministic filesystem-safe text slug, preserves the declared maximum length, and preserves the canonical non-empty fallback.

`space_normalize` preserves the approved `None` to empty-string behavior and collapses supported whitespace and NUL separators to single ASCII spaces.

`space_normalize_and_lower` applies `space_normalize` before the existing lowercase transformation.

## Dependency boundary

Generic standard-library modules do not depend on project submodules or project configuration. HTML, image, and S3 dependencies remain isolated in their owning modules and are declared by each consumer that imports those modules.

`utils` does not depend on `config_logging`, `ConfigBoto3`, application environment keys, marketplace entities, or application-specific storage layout.
```

  - approved protected consumer wording:

```md
- `utils`
  - Root path: `utils/`.
  - Host contract: `utils/DESIGN.md`.
```

  - version and `Legacy` boundary:
    - canonical provider development starts from current `origin/main`, removes the reverted marketplace-specific content again, and does not merge the `marketplace_tools` domain branch into canonical `main`;
    - this instruction migration does not force application modernization in `Legacy` consumers solely to update a submodule pointer;
    - a `Legacy` consumer may retain its pinned historical revision while its application behavior remains outside an explicitly requested migration;
    - protected current consumers move to the canonical provider only after their directly imported public surface and tests are compatible;
    - any future consumer migration that needs removed marketplace behavior must first move that behavior to an approved actual owner and must not restore it in canonical `utils`;
    - future migration of one current LLM test workflow follows the separately approved `Legacy LLM test normalization lifecycle`;
  - implementation and compatibility acceptance:
    - verify the canonical module public interface contains exactly the approved names and the package root does not re-export them;
    - verify camel/Pascal single-name conversion and recursive non-mutating JSON key conversion across mappings, lists, scalars, empty collections and nested mixed structures;
    - verify aware UTC output, preserved default microseconds, explicit valid microsecond replacement and invalid microsecond failure;
    - verify atomic file publication, cleanup after failure, same-filesystem path replacement and parent-directory synchronization;
    - verify temporary namespace and relative-path acceptance plus rejection of empty namespace, absolute path, parent traversal and symlink escape;
    - verify independent namespaces and explicit run-specific namespaces do not collide;
    - verify HTML parser, image-processing and S3 dependencies remain isolated to their defining modules;
    - verify image resource closure, target `Path` return and quality validation;
    - verify S3 success, not-modified, missing-object, authorization failure, transport failure and one-request conditional download behavior;
    - verify exact invisible-symbol, SHA-256, slug fallback and maximum-length, whitespace, NUL, `None` and lowercase semantics;
    - verify canonical provider modules do not import `config_logging`, `ConfigBoto3`, project environment keys or marketplace entities;
    - remove `pydantic.py`, `json_hash.py`, application string normalization and datetime-version helpers only after confirming their absence from canonical consumers;
    - remove marketplace offer-image functions from canonical `utils` without deleting them from pinned `Legacy` revisions;
    - remove LLM normalization from canonical `utils` while preserving the historical pinned revisions used by unchanged `Legacy` tests;
    - update protected consumer gitlinks only after direct provider tests and each consumer's applicable compatibility tests pass.

### Batch 22: Legacy LLM test normalization lifecycle

Пользователь явно утвердил этот batch 24 июля 2026 года. Отдельный active provider для current LLM test normalization не создаётся: helper остаётся только в исторических revisions двух неизменяемых `Legacy` consumers и удаляется из canonical `utils`.

- `B22-1 LLM test normalization owner`
  - source: `utils.llm_test_utils.normalize_llm_translations` в revisions `b849f2ebd409772b9313b4301032374129391448` и `c291ba375a080cb4fc92ab0c96ae9072693d19fd`, а также его неиспользуемая current rewrite `utils.llm_test_util.llm_translation_normalize`;
  - verified consumers:
    - helper используется ровно четырьмя tests:
      - `marketplace-parser/base_marketplace_parser/test/test_translate_into_russian/test_translate_into_russian.py`;
      - `marketplace-tr/offer_data_translator/test/test_translate_into_russian/test_translate_into_russian.py`;
      - `marketplace-tr/trendyol/tests/test_size_chart_parser/test_size_chart_parser.py`;
      - `marketplace-tr/offer_dimensions_generate/tests/test_offer_dimensions_generator/test_offer_dimensions_generator.py`;
    - `marketplace-parser` и `marketplace-tr` не имеют current canonical `AGENTS.md`, используют unmodeled root code и классифицируются как `Legacy` без явно запрошенной code migration;
    - все четыре tests используют historical tuple-returning API `normalize_llm_translations`;
    - current `LlmTranslationNormalizeResult` и `llm_translation_normalize` не имеют external consumers;
    - `workflow-control-center`, `marketplace-tr-priority` и их `openai_cached` submodules не содержат equivalent consumer или helper;
    - оба legacy projects используют `openai_agent`, но normalization helper не владеет transport, request, billing или persistence behavior этого provider и только форматирует project test artifacts;
    - original commit `173c0955259d8e6e6f71e204ed826cf03fc030bb` прямо добавил helper для consistency и readability unit-test output;
  - owner decision:
    - canonical shared owner сейчас отсутствует и не создаётся;
    - `openai_agent`, `openai_cached`, `agent-workflows`, `project-standards` и новый standalone provider не получают helper;
    - historical pinned `utils` revisions остаются владельцами существующего неизменяемого `Legacy` behavior только в пределах этих pins;
    - canonical `utils/main` удаляет `llm_test_util.py` и не объявляет compatibility alias;
  - `Legacy LLM test normalization lifecycle`:
    - эта goal не обновляет gitlinks, imports, return shape или test artifacts двух `Legacy` projects только ради provider convergence;
    - явная будущая modernization одного текущего test workflow сначала переносит normalization в owner-local `test` support этого workflow или его реального project-local shared test owner;
    - первый migrated consumer не создаёт новый cross-project provider;
    - появление второго non-`Legacy` consumer с доказанно одинаковой stable normalization semantics становится обязательным trigger для extraction в один actual shared test-support owner;
    - extraction выполняется в change set второго consumer и удаляет owner-local duplicate первого consumer;
    - заранее создавать provider, plugin skill, public runtime module или future-use abstraction запрещено;
    - canonical `utils` не получает helper обратно ни на одном lifecycle step;
  - amendment to `B21-1 utils`:
    - прежнее условие об обязательном separate owner до удаления LLM helper заменено этим lifecycle;
    - canonical removal и historical pin preservation не считаются application behavior change;
  - verification:
    - confirm canonical `utils` public inventory and source tree contain no LLM normalization module, class, function or alias;
    - confirm the four historical test imports and their pinned submodule revisions remain unchanged during this goal;
    - confirm protected modern projects and `openai_cached` have no affected import or equivalent duplicated helper;
    - confirm project standardization classifies the current unmodeled application roots in `marketplace-parser` and `marketplace-tr` as `Legacy` and does not silently migrate them;
    - fail a future second non-`Legacy` duplicate unless the same change extracts one actual shared owner.

### Batch 23: config_sqlalchemy

Пользователь явно утвердил этот batch 24 июля 2026 года с обязательным уточнением: migration каждого call site выполняется только после анализа его реального поведения и owner context. Механическая массовая замена имён запрещена; refactor обязан сохранить или осознанно исправить конкретные engine, session, transaction, concurrency и lifecycle semantics каждого места.

- `B23-1 config_sqlalchemy`
  - sources: block `config_sqlalchemy` в `Standard Submodule List` обоих protected `AGENTS.md`;
  - source state: blocks byte-identical, SHA-256 `b334b07a2856a26705523cdc418d8360c594db1d2060b6f162399ded218dfd7a`;
  - verified consumer state:
    - шесть consumers используют пять distinct revisions:
      - `marketplace-next-co-uk` использует `5f9e2dcb14dd900499f444ecba39156fc2b5a8d8`;
      - `marketplace-parser` и `marketplace-tr` используют `77598cdcf15658cbfdf9bb5c4dfec79b1d290cba`;
      - `marketplace-tools` использует branch revision `17aa70b513652dd34930d77f748737317f18726b`;
      - `marketplace-tr-priority` использует canonical `origin/main` revision `d054bb1db4aaad515bb52716824bb731d8b9ae49`;
      - `workflow-control-center` использует ancestor revision `c12e7a6bc45f92ba7d93184568e98bbd1e1554f7`;
    - canonical `origin/main` уже использует names `engine_get` и `session_get`, поддерживает несколько engine bindings, применяет MySQL и PostgreSQL port defaults и включает `pool_pre_ping`;
    - older revisions используют несовместимые combinations `create_engine`, `create_session_maker`, `engine` и `session`;
    - branch revision `marketplace-tools` содержит реально используемые generic pool settings `pool_recycle` и `pool_use_lifo`, отсутствующие в canonical `main`;
    - current canonical implementation строит URL строковой конкатенацией, сохраняет raw `query_args`, считает explicit `''` и `0` отсутствующими, не синхронизирует cache creation, создаёт новый `sessionmaker` при каждом `session_get` и не защищает inherited pool при `fork`;
    - `marketplace-tr-priority` содержит реальный `fork` workflow, поэтому inherited connection-pool safety является current requirement, а не future-use safeguard;
    - реальные modern call sites используют `database`, `engine_name`, `pool_size`, `pool_max_overflow` и `expire_on_commit`;
    - `marketplace-tools` задаёт `SqlAlchemyPoolSize`, `SqlAlchemyPoolMaxOverflow`, `SqlAlchemyPoolRecycle` и `SqlAlchemyPoolUseLifo`;
    - ни один current SQLAlchemy consumer не использует async engine или `AsyncSession`;
    - ни один runtime or deployment consumer не задаёт `SqlAlchemyQueryArgs`;
    - `marketplace-tr/.gitmodules` содержит две sections для одного path `config_sqlalchemy/`;
  - provider target owner: `config_sqlalchemy/DESIGN.md` в repository `submodule-config-sqlalchemy`;
  - applicability owner: `project-standards:sqlalchemy-developer`;
  - transformation:
    - canonical development starts from current `origin/main`;
    - generic pool recycle and LIFO behavior from the `marketplace-tools` branch переносится семантически без mechanical branch merge;
    - process-global classmethod API заменяется одним canonical package-root instance `sqlalchemy_config`, который владеет реальным process-local cache state через instance methods;
    - URL создаётся через `sqlalchemy.URL.create`, а raw URL assembly и неиспользуемый raw `query_args` удаляются;
    - configuration resolution использует утверждённый `config_env` contract, сохраняет explicit `''`, `0` и `-1`, а `None` использует только как выделенное значение отсутствующего override;
    - `Engine` и `sessionmaker` caches становятся thread-safe и process-local;
    - inherited engines в forked child получают `dispose(close=False)` до child use, после чего child caches создаются заново;
    - старый `SqlAlchemyPoolRecycle` мигрирует в unit-explicit `SqlAlchemyPoolRecycleSeconds`;
    - старые public names и compatibility aliases не сохраняются;
    - async API не создаётся без current consumer;
    - consumer-local block заменяется exact provider reference без project-local specialization;
  - approved provider wording:

```md
# config_sqlalchemy

## Purpose

`config_sqlalchemy` owns process-local SQLAlchemy `Engine` and `sessionmaker` construction, validated environment-backed connection configuration, connection-pool reuse, and fork-safe cache lifecycle.

It does not own ORM models, project database registries, schema bootstrap, migrations, transaction boundaries, or production-versus-test database selection.

## Public interface

The canonical package-root public interface consists only of `sqlalchemy_config`.

`sqlalchemy_config.engine_get(...)` returns a process-local cached `Engine`.

`sqlalchemy_config.session_get(...)` returns a new `Session` from the cached session factory.

`SqlAlchemyConfig`, `EngineConfig`, `engine`, `session`, `create_engine`, `create_session_maker`, compatibility aliases, and alternate public import paths are not part of the package-root interface.

## Configuration contract

Explicit non-`None` arguments take precedence over environment values. Environment values take precedence over provider defaults.

`None` means that no explicit override was supplied. Explicit `''`, `0`, and `-1` values are preserved and validated instead of being replaced by environment values or defaults.

The environment contract consists of:

- `SqlAlchemyDatabase`;
- `SqlAlchemyDialect`;
- `SqlAlchemyDriver`;
- `SqlAlchemyHost`;
- `SqlAlchemyPassword`;
- `SqlAlchemyPoolMaxOverflow`;
- `SqlAlchemyPoolRecycleSeconds`;
- `SqlAlchemyPoolSize`;
- `SqlAlchemyPoolUseLifo`;
- `SqlAlchemyPort`;
- `SqlAlchemyUser`.

Configuration reading uses the canonical `config_env` typed readers.

The defaults are:

- `database=""`;
- `engine_name=""`;
- `pool_max_overflow=0`;
- `pool_recycle_seconds=-1`;
- `pool_size=1`;
- `pool_use_lifo=false`;
- MySQL port `3306`;
- PostgreSQL port `5432`.

A dialect without a known default requires an explicit port.

`pool_size` accepts values greater than or equal to `0`. `pool_max_overflow` and `pool_recycle_seconds` accept values greater than or equal to `-1`.

Raw URL fragments, raw query strings, arbitrary `create_engine` keyword forwarding, and arbitrary `sessionmaker` keyword forwarding are not part of the interface.

## Engine contract

Connection URLs are constructed only through `sqlalchemy.URL.create`.

`pool_pre_ping` is always enabled. `pool_recycle_seconds` and `pool_use_lifo` preserve the corresponding standard SQLAlchemy pool semantics.

Within one process, the same resolved configuration and `engine_name` return the same `Engine`. A different `engine_name` creates an independent engine and connection pool.

Engine creation and cache mutation are thread-safe.

Configuration values, connection URLs containing credentials, and passwords are never written to ordinary logs or exception messages.

## Process contract

No environment value is read and no engine or connection is created during package import.

Before a forked child uses an inherited cache, every inherited engine is disposed with `close=False`, and the child creates fresh engine and session-factory caches.

A child process never checks out a connection inherited from its parent.

## Session contract

One `sessionmaker` is cached for each engine and supported session configuration. The supported session-specific override is `expire_on_commit`.

Every `session_get` call returns a new `Session`. A `Session` is never cached, shared between threads, or shared between processes.

The caller owns the session context, transaction boundary, commit, rollback on failure, and close.

## Host integration

The root bootstrap calls `config_env.env_config_load` before the first `sqlalchemy_config` operation.

A project database owner resolves stable project database keys and production-versus-test selection, then passes the physical database name explicitly.

A Python `Submodule` does not hardcode host project database identifiers. Its database binding remains dynamic through the host environment or an explicit binding supplied by the host.

Direct `Engine` access is reserved for owners that actually require engine-level operations such as database bootstrap or migration tooling.
```

  - approved protected consumer wording:

```md
- `config_sqlalchemy`
  - Root path: `config_sqlalchemy/`.
  - Host contract: `config_sqlalchemy/DESIGN.md`.
```

  - call-site semantic migration gate:
    - before changing one call site, record which old operation it uses, every supplied positional and keyword argument, the result consumer, session or factory lifetime, transaction owner, close behavior, thread or process context, and whether the call participates in project bootstrap, schema operations, application work, tests, callbacks or framework integration;
    - distinguish direct `Session` creation, context-managed `Session` use, manually closed sessions, intentionally long-lived sessions, externally retained `sessionmaker` factories and direct `Engine` use; syntactic similarity does not make these behaviors interchangeable;
    - a `create_session_maker` consumer is refactored according to why it retains a factory; it MUST NOT be replaced blindly with one eagerly created `Session`;
    - an `engine` consumer used for `metadata.create_all`, migration setup, connection-level work or framework binding remains an engine-level consumer instead of being rewritten through a session only to match the common case;
    - existing transaction, commit, rollback, expiration and close semantics are traced through the complete caller flow before changing ownership;
    - thread-local and process-local behavior is verified at the real concurrency boundary; adding `engine_name` or changing it is not used as an unproven substitute for lifecycle analysis;
    - monkeypatch targets, fixtures and tests are updated according to the final real owner instead of preserving old symbol locations through aliases;
    - bulk search-and-replace, AST-only rename, import-only rewrite or another mechanical transformation is forbidden as the migration method;
    - when analysis reveals an existing resource leak, inherited connection hazard, missing close or incorrect transaction boundary, the implementation fixes the real owner and adds direct regression coverage instead of preserving the defect or hiding it behind the new API;
    - each changed call site must be explainable in terms of preserved or deliberately corrected behavior, and each consumer repository handoff must include the relevant direct runtime or test evidence;
  - implementation and compatibility acceptance:
    - verify the package-root public API contains exactly `sqlalchemy_config`;
    - verify package import performs no environment read, engine creation or DB connection;
    - verify explicit, environment and default precedence for every supported field, including preservation of `''`, `0` and `-1`;
    - verify required-field, integer, boolean, port and pool-range failures identify only the configuration key and never disclose its value;
    - verify `sqlalchemy.URL.create` correctly handles representative reserved characters in username and password without manual encoding or credential disclosure;
    - verify MySQL, PostgreSQL, unknown-dialect explicit-port and database-empty URL behavior;
    - verify fixed `pool_pre_ping`, defaults `pool_size=1`, `pool_max_overflow=0`, `pool_recycle_seconds=-1` and `pool_use_lifo=false`;
    - verify the migrated `marketplace-tools` pool values preserve its current `3`, `2`, `1800` and `true` behavior through the final environment names;
    - verify same-config same-name engine reuse, different-name isolation, different-database isolation and exactly one engine creation under concurrent first access;
    - verify one cached `sessionmaker` per supported configuration and a distinct new `Session` on every call;
    - verify `expire_on_commit` behavior and confirm `session_get` does not commit, rollback or retain the returned session;
    - verify a forked child disposes inherited engines with `close=False`, creates a new pool and never checks out a parent connection;
    - verify spawned processes create independent caches without relying on fork cleanup;
    - verify credentials never appear in cache representations, errors, logs or test diagnostics;
    - remove raw `query_args`, arbitrary kwargs, old public names and alternate import paths only after the semantic call-site inventory proves no required behavior depends on them;
    - remove the duplicate `marketplace-tr/.gitmodules` section while preserving the single canonical `config_sqlalchemy/` gitlink;
    - update only a separately approved non-`Legacy` consumer gitlink after its call-site inventory, directly affected behavior tests and applicable repository verification pass;
    - preserve `Legacy` consumer call sites and gitlinks when migration would change their existing environment-loading semantics.

### Batch 24: retry_runtime

Пользователь явно утвердил этот batch 24 июля 2026 года. Refactor и replacement каждого call site выполняются только после анализа реального поведения конкретной операции. Механическая замена decorators, imports, field names или HTTP adapter arguments запрещена.

- `B24-1 requests_retry -> retry_runtime`
  - sources: block `requests_retry` в `Standard Submodule List` обоих protected `AGENTS.md`;
  - source state: blocks byte-identical, SHA-256 `f38d391a65d37ff93d8561f20563ef8db9bee14d4b2ec629d6f659fb02ed65a1`;
  - verified consumer state:
    - шесть consumers используют пять distinct revisions:
      - `workflow-control-center` использует `7db19f831496322167db8dc6403496a8b92faa3a`;
      - `marketplace-tr-priority` использует canonical `origin/main` revision `1f70c806cdb5fd537ea59fd357edcce1c7cc058f`;
      - `marketplace-tools` использует branch revision `8d4dcb8a4fe060d66a93f93c857d225cd0ccba56`;
      - `marketplace-next-co-uk` использует `57be9860eceaab15dabca67ebd44a9c40dd48d96`;
      - `marketplace-parser` и `marketplace-tr` используют `dd36957f069a7e47bad76f497c4ae598777e1d79`;
    - current generic `RetryConfig.count` означает общее число executions, включая первый, а `urllib3.Retry.total` означает число retries после первого execution;
    - current generic `RetryConfig.backoff` является множителем предыдущей задержки, а HTTP adapter передаёт то же значение как `urllib3` `backoff_factor`, который использует другую формулу;
    - current generic default attempt count равен `5`, а older revisions используют explicit or default infinite mode `-1`;
    - current `retry` и `async_retry` по умолчанию ловят `BaseException`; special case существует только для `KeyboardInterrupt`, поэтому `SystemExit`, `GeneratorExit` и `asyncio.CancelledError` могут повторяться;
    - current `auto_retry` через reflection оборачивает все non-dunder methods одного класса, включая state-mutating `add`, `flush` и inherited or overriding API methods;
    - `OzonAgent` и его buffered helpers создают nested retry layers: class-wide wrapper, method-level wrapper и HTTP adapter могут владеть одной фактической операцией одновременно;
    - некоторые buffered helpers очищают buffer в `finally`, поэтому повтор outer operation может выполняться уже с изменённым или пустым state;
    - current HTTP factory задаёт `allowed_methods=None`, поэтому разрешает transport retry любого HTTP method;
    - current HTTP factory передаёт generic attempt count напрямую в `urllib3.Retry.total`, создавая на один HTTP execution больше generic policy при конечном count;
    - canonical HTTP factory не гарантирует timeout, а branch `marketplace-tools` добавляет default timeout через `RequestTimeoutConfig`;
    - branch `RequestTimeoutConfig` реально используется не только `requests`, но также Boto3, SMTP, OpenAI, Playwright и generated Yandex client;
    - одинаковые environment keys `ServiceConnectionTimeout` и `ServiceResponseTimeout` используются несколькими projects с разными defaults и разной transport semantics, поэтому совпадение key names не доказывает существование одного общего timeout object;
    - generic explicit `jitter` и `max_delay` consumers отсутствуют;
    - modern `openai_cached` consumers используют exception-based retry, result-based retry неполных buffered responses, exact attempt numbers и owner-specific logging;
    - current `WorkflowControlCenterBrandSizeChartClient` использует через одну retry-enabled Session как safe `GET`, так и potentially non-idempotent `POST` для resource creation, file upload и `WorkflowRun` creation;
    - current image-download consumers используют retry-enabled Session только для `GET`;
  - standards evidence:
    - Python documentation определяет `asyncio.CancelledError` как `BaseException`, которое почти всегда должно быть re-raised;
    - `urllib3` documentation определяет `total` как число retries, отдельно предупреждает о possible side effects после read error и использует только idempotent method set по умолчанию;
    - Requests documentation подтверждает отсутствие timeout, когда caller его явно не задаёт;
    - Tenacity поддерживает sync and async retry controllers, exception and result predicates и context-manager loops, но его adoption не устраняет owner-specific state, result и idempotency decisions текущих consumers;
  - owner decision:
    - второй generic retry provider не создаётся;
    - repository, package и `Submodule` path получают целевое имя `retry_runtime`;
    - remote repository rename является отдельным publication action, но final consumer state не сохраняет permanent mismatch между repository, package и path names;
    - один provider содержит два явно разделённых contracts:
      - generic operation retry policy;
      - qualified `Requests` transport integration;
    - generic и HTTP configs не наследуются друг от друга и не разделяют field, если его semantics различается;
    - Tenacity не добавляется: target provider сохраняет только небольшой прозрачный policy object, а exception, result, state и logging decisions остаются у фактического operation owner;
    - cross-protocol `RequestTimeoutConfig` не переносится как будто это один requests-owned contract; каждый non-Requests consumer проходит отдельный semantic owner analysis;
  - provider target owner: `retry_runtime/DESIGN.md` в repository с целевым именем `submodule-retry-runtime`;
  - applicability owner: `project-standards:python-retry-developer`;
  - transformation:
    - `RetryConfig.count` переименовывается в `attempt_count`;
    - `RetryConfig.backoff` переименовывается в `backoff_multiplier`;
    - `RetryConfig.delay` переименовывается в `delay_seconds`;
    - `RetryConfig` становится immutable explicit policy object без process-global cache;
    - `RetryConfig` владеет только attempt iteration, next-attempt decision и delay computation;
    - environment-backed construction происходит явно после `config_env.env_config_load`;
    - generic decorators `retry`, `async_retry` и `auto_retry` удаляются;
    - free runtime helpers `next_retry_attempt_exist`, `retry_attempt_iter` и `retry_delay_second_compute` заменяются receiver-owned `RetryConfig` behavior;
    - unused `jitter` и `max_delay` удаляются без future-use replacement knobs;
    - HTTP integration получает отдельные `RequestsRetryConfig` и `RequestsTimeoutConfig`;
    - `create_requests_session_with_retry` и `requests_session_with_retry_create` заменяются одним package-root name `requests_session_create`;
    - HTTP retry count отображается в `urllib3` через `total=attempt_count-1`;
    - HTTP methods и status codes задаются typed sets вместо `allowed_methods=None`, `status_forcelist` и arbitrary kwargs;
    - consumer-local block `requests_retry` заменяется exact `retry_runtime` provider reference;
    - old path, package, public names и compatibility aliases не сохраняются;
  - approved capability wording:

```md
# python-retry-developer

Use when Python code adds or changes retry policy, retry loops, backoff, retryable exception or result classification, HTTP transport retries, or one operation that can execute more than once after an ambiguous failure.

The provider `retry_runtime/DESIGN.md` owns the shared retry-policy and Requests transport interfaces. A project that implements retry behavior MUST use that provider contract unless the user explicitly requires a project-local exception.

One retry boundary encloses the smallest complete atomic operation whose repetition is proven safe. Exactly one runtime layer owns retry for one external operation.

Before enabling retry, the operation owner proves which failures are transient, which side effects may already have happened, whether repetition is idempotent, how partial state is restored or preserved, and which timeout bounds each attempt.

Class-wide automatic retry, reflection-based method wrapping, nested retry layers for the same operation, implicit retry of every exception, and implicit retry of non-idempotent methods are forbidden.

Retry catches only explicitly selected subclasses of `Exception`. `BaseException`, process-control exceptions, generator termination, and async cancellation are never retry policy inputs.

Attempt count always includes the first execution. Names for delays, multipliers, factors, and timeouts state their real unit or mathematical role.

Retries do not log credentials, payloads, URL user information, URL query strings, secret-bearing paths, or raw exception text that may contain external data.
```

  - approved provider wording:

```md
# retry_runtime

## Purpose

`retry_runtime` owns one explicit generic retry policy and one qualified Requests transport integration.

It does not own operation-specific transient-error classification, result acceptance, side-effect rollback, idempotency decisions, API rate-limit policy, business-state recovery, or cross-protocol service timeout configuration.

## Public interface

The canonical package-root public interface consists only of:

- `RequestsRetryConfig`;
- `RequestsTimeoutConfig`;
- `RetryConfig`;
- `requests_session_create`;
- `retry_cli_args_add`.

Generic retry decorators, class decorators, reflection helpers, free attempt-loop helpers, internal Session subclasses, internal `urllib3.Retry` subclasses, compatibility aliases, and alternate public import paths are not part of the interface.

## Generic retry config

`RetryConfig` is one immutable validated policy with:

- `attempt_count`;
- `backoff_multiplier`;
- `delay_seconds`.

`attempt_count` includes the first execution. Its default is `5`; it accepts `-1` for an explicitly infinite policy or a value greater than or equal to `1`. Zero and values below `-1` are invalid.

`backoff_multiplier` has default `1` and accepts values greater than or equal to `1`.

`delay_seconds` has default `0` and accepts values greater than or equal to `0`.

The delay before retry number `n`, where the first retry has number `1`, is `delay_seconds * backoff_multiplier ** (n - 1)`.

`RetryConfig.attempt_number_iter()` yields one-based execution attempt numbers. `RetryConfig.can_retry(attempt_number=...)` reports whether another execution is allowed. `RetryConfig.delay_seconds_get(retry_number=...)` returns the delay before one-based retry number.

These methods do not catch exceptions, inspect results, sleep, log, mutate owner state, or invoke the operation. The operation owner retains those decisions explicitly.

`RetryConfig.from_env()` reads only:

- `RetryAttemptCount`;
- `RetryBackoffMultiplier`;
- `RetryDelaySeconds`.

It reads only the already-loaded process environment, does not load dotenv files, does not depend on one host
`config_env` revision, and does not cache the result. This keeps the provider usable by hosts whose approved
environment bootstrap interfaces differ while leaving environment-file precedence entirely with each host.

Package import reads no environment value and creates no runtime policy singleton.

## Generic CLI contract

`retry_cli_args_add` adds one aggregate `RetryConfig` destination with:

- `--retry-attempt-count`;
- `--retry-backoff-multiplier`;
- `--retry-delay-seconds`.

One scope prefixes all three names, for example `--page-retry-attempt-count`.

Environment binding follows `ConfigArgumentParser` and its generated scoped environment names. The aggregate action uses `ConfigArgumentParser.env_key_get`; it does not disable environment binding or implement a second environment-key algorithm.

Value precedence is explicit CLI input, generated scoped environment value, caller-supplied aggregate default, unscoped `RetryConfig.from_env()` value, and provider default.

Help states that attempt count includes the first execution and that `-1` means an explicitly infinite policy. It identifies multiplier and second units exactly.

## Requests retry config

`RequestsRetryConfig` is independent from `RetryConfig`. It contains:

- finite `attempt_count`, with default `5` and minimum `1`;
- `backoff_factor_seconds`, with default `1` and minimum `0`;
- immutable `allowed_method_set`;
- immutable `status_code_set`.

The default allowed method set is `DELETE`, `GET`, `HEAD`, `OPTIONS`, `PUT`, and `TRACE`. The default status-code set is empty.

`POST`, `PATCH`, or another method outside the default set is added only by the operation owner after proving idempotency, a valid idempotency-key contract, or another endpoint-specific safe repetition contract.

`requests_session_create` maps total executions to `urllib3.Retry.total` as `attempt_count - 1`. Passing generic `RetryConfig`, `allowed_methods=None`, a negative HTTP attempt count, raw `urllib3` kwargs, or an arbitrary adapter object is forbidden.

Connection failures known to occur before request transmission may use the configured retry budget. Read, status, or other failures that may happen after request transmission do not make a non-idempotent method retryable.

HTTP `Retry-After` handling retains the standard `urllib3` behavior. Redirect policy, unknown-error policy, and exhaustion behavior are fixed by the provider and are not arbitrary consumer kwargs.

Provider-owned retry records may identify HTTP method, response status, exception class, completed attempt number, and next attempt number. They never contain URL user information, URL query strings, credentials, request or response payloads, or raw exception text.

## Requests timeout config

`RequestsTimeoutConfig` contains positive finite `connect_timeout_seconds` and `read_timeout_seconds`.

`RequestsTimeoutConfig.from_env()` reads:

- `RequestsConnectTimeoutSeconds`, default `10`;
- `RequestsReadTimeoutSeconds`, default `30`.

`requests_session_create` requires explicit `RequestsRetryConfig` and `RequestsTimeoutConfig` objects. The returned Session applies the timeout config whenever one request does not provide a narrower finite override. An explicit unlimited timeout is forbidden.

Connect timeout, read timeout, whole-operation deadline, Playwright timeout, SMTP timeout, Boto3 timeout, OpenAI timeout, and generated-client timeout remain distinct boundary semantics. Similar old environment names do not merge those contracts.

## Session lifecycle

Every `requests_session_create` call returns a new Session. A Session is not cached process-globally and is not shared across processes.

The caller owns Session injection, thread confinement or synchronization, context management, and close.

The provider does not retry a consumer method around a Session request and does not add a second retry layer above the configured HTTP adapter.

## Host integration

The root bootstrap loads environment before constructing configs and injects resolved configs into the actual operation owner.

One operation owner may choose generic retry or Requests transport retry according to the real boundary. It does not stack both around the same request unless the two layers own proven distinct complete operations.
```

  - approved protected consumer wording:

```md
- `retry_runtime`
  - Root path: `retry_runtime/`.
  - Host contract: `retry_runtime/DESIGN.md`.
```

  - call-site semantic migration gate:
    - before changing one call site, record the callable or loop, every supplied retry argument, actual execution count semantics, delay sequence, retryable exception or result condition, timeout behavior, mutable state touched before failure, external side effects, and every inner or outer retry layer;
    - classify whether the repeated unit is a pure computation, read, idempotent write, non-idempotent write, buffered state transition, polling iteration, multipart upload, generated-client call or multi-step workflow;
    - identify the exact atomic operation rather than assuming the existing decorator boundary is correct;
    - prove which owner logs, sleeps, restores partial state and decides whether a result is acceptable;
    - preserve owner-specific result-based loops such as unresolved OpenAI buffered items through explicit `RetryConfig` attempt behavior rather than forcing exception-only control flow;
    - replace `BaseException` with the actual transient `Exception` subclasses or owner predicate after inspecting the external library and current failure handling;
    - verify cancellation, `KeyboardInterrupt`, `SystemExit` and generator termination leave immediately without another attempt;
    - remove every nested retry layer that repeats the same external operation; a lower transport retry and a higher business retry may coexist only when they own distinct, independently proven atomic operations;
    - for every HTTP call, distinguish connect-before-send failure from read, status or ambiguous after-send failure;
    - do not add `POST` or `PATCH` to `allowed_method_set` from method name alone; inspect endpoint semantics, idempotency keys, server behavior and caller recovery;
    - `WorkflowControlCenterBrandSizeChartClient.workflow_run_start`, DataSource creation and file upload remain non-retried after an ambiguous transmission until their API contracts provide safe idempotency;
    - safe image and report `GET` downloads preserve retry after timeout and Session-lifecycle analysis;
    - `OzonAgent` and each buffered helper are reviewed method-by-method:
      - `add` does not receive retry merely because its class previously used `auto_retry`;
      - retry around `flush` requires a stable payload snapshot and explicit buffer retention or release semantics for success, definitive rejection and ambiguous failure;
      - nested `OzonAgent`, `BaseApiAgent` and HTTP adapter attempts are reduced to the intended single owner for each request;
    - `RequestTimeoutConfig` consumers in Requests, Boto3, SMTP, OpenAI, Playwright and generated clients are classified independently; syntactic reuse of the same two environment keys is not accepted as semantic proof;
    - bulk search-and-replace, AST-only decorator removal, import-only rewrite or global exception-type substitution is forbidden as the migration method;
    - each changed call site must be explainable in terms of preserved or deliberately corrected execution count, side effects, state, timeout and final error behavior;
  - implementation and compatibility acceptance:
    - verify repository, package, submodule path, documentation and protected references converge on `retry_runtime` without compatibility paths or aliases;
    - verify package-root public API contains exactly the approved names;
    - verify package import reads no environment, creates no config singleton and mutates no class;
    - verify `RetryConfig` defaults, strict validation, explicit `-1`, one-based attempt numbers, next-attempt decisions and exact delay formula;
    - verify `attempt_count=1` executes once without retry and finite values produce exactly that many total executions;
    - verify generic environment names, CLI names, scoped generated environment names, precedence and unit-explicit help;
    - verify malformed present integer or float values fail without exposing their values;
    - verify generic policy methods do not catch, sleep, log or invoke consumer operations;
    - verify `retry`, `async_retry`, `auto_retry`, reflection markers, `jitter`, `max_delay` and old free runtime helpers are absent;
    - verify `RequestsRetryConfig` rejects infinite or zero attempts and maps `1` to `urllib3 total=0` and `5` to `urllib3 total=4`;
    - verify default HTTP methods and empty status set, plus rejection of `allowed_methods=None` and arbitrary `urllib3` kwargs;
    - verify connection, read, status, `Retry-After`, exhaustion and non-idempotent method behavior with exact request counts;
    - verify `POST` and `PATCH` are not retried after ambiguous send under defaults;
    - verify safe explicit opt-in only for one test endpoint with a proven idempotency contract;
    - verify default connect and read timeouts, explicit finite override and rejection of unlimited timeout;
    - verify each factory call returns a distinct Session and caller close behavior remains effective;
    - verify provider-owned and configured third-party retry logs do not disclose URL user information, URL query strings, credentials, payloads or raw exception text;
    - verify async cancellation and every control-flow exception propagates without another attempt;
    - add regression coverage for exact call count and buffer state for representative Ozon `add`, successful `flush`, definitive rejection and ambiguous `flush` failure;
    - add regression coverage proving `WorkflowRun` creation and file upload are not duplicated by transport retry;
    - add regression coverage proving safe GET downloads retain the intended retry count and timeout;
    - update every consumer gitlink only after its call-site inventory, directly affected behavior tests and applicable repository verification pass.

### Batch 25: base_api_agent retirement

Пользователь явно утвердил этот batch 24 июля 2026 года. `base_api_agent` не переносится в новый provider и не переименовывается: после semantic migration всех consumers submodule, package и public class удаляются без compatibility layer.

- `B25-1 base_api_agent retirement`
  - sources: block `base_api_agent` в `Standard Submodule List` обоих protected `AGENTS.md`;
  - source state: blocks byte-identical, SHA-256 `c77a7d703c3b5ad645b87c573dc1569c49ccd4491a61aeca94f0a965dbb326a2`;
  - verified consumer state:
    - четыре direct consumers используют три distinct revisions repository `submodule-base-api-agent`:
      - `workflow-control-center` и `marketplace-tr-priority` используют main revision `654100c07e8425c04fe9af2d03eb05cb57a08046`;
      - `marketplace-tools` использует divergent branch revision `3c38e153952973597aec38d4fad2ee9ba8ba4bc9`;
      - `marketplace-tr` использует common ancestor revision `3244c11ed5a4fee483039f2f82a109afd96028d1`;
    - main revision не содержит provider tests, а divergent branch содержит только narrow logging and timeout tests;
    - `BaseApiAgent` одновременно владеет Session construction, timeout, transport retry, generic HTTP verb dispatch, URL joining, JSON decoding, 204 handling, logging, 429 waiting и mutable rate-limit state;
    - каждый generic HTTP verb обёрнут generic retry, а current `workflow-control-center` и `marketplace-tr-priority` revisions `OzonAgent` дополнительно используют class-wide `auto_retry`;
    - current Ozon client имеет один direct `GET`, двадцать шесть direct `POST` call sites и дополнительные mutation helpers, которые вызывают public generic `post`;
    - Ozon `POST` operations включают как read-like queries, так и report creation, chat actions, cancellation, product import, price update, stock update, archive and unarchive mutations;
    - current Wildberries client имеет восемнадцать direct `GET`, одиннадцать direct `POST` и один direct `PATCH` call site;
    - `WbAgent.move_order_to_package()` передаёт `base_marketplace_url` как payload в inherited `patch()` и не передаёт обязательный `base_url`, поэтому current call fails before an HTTP request;
    - generic 429 behavior использует Wildberries-specific response headers `X-Ratelimit-Remaining` и `X-Ratelimit-Retry`;
    - wakeup state keyed only by `base_url`, хотя Wildberries определяет limits для конкретных API methods and quota categories;
    - `Response.json()` failure raises `requests.exceptions.JSONDecodeError`, который является `ValueError`, поэтому current broad `except ValueError` ошибочно классифицирует invalid JSON как 429;
    - current code не разделяет unexpected HTTP status, provider error payload и invalid JSON;
    - 204 behavior расходится между main и divergent branch revisions;
    - Session открывается скрыто, доступна consumers как mutable `session` or `_session`, используется напрямую для headers and image download и не имеет системно определённого close owner;
    - generic public verbs используются внутри `ozon_agent` and `wb_agent`, но direct host-project production use этих verbs вне owning integration submodules в проверенных consumers не найден;
  - standards evidence:
    - HTTP semantics не рекомендует автоматически повторять non-idempotent request без знания, что конкретная operation idempotent, или доказательства, что initial request не был применён;
    - Requests Session уже владеет connection pooling, persistent request configuration, context-manager lifecycle и explicit `close()`;
    - Wildberries documentation определяет token-bucket rate limits per API method or category и response headers `X-Ratelimit-Remaining`, `X-Ratelimit-Retry`, `X-Ratelimit-Limit` и `X-Ratelimit-Reset`;
  - owner decision:
    - `base_api_agent` не получает `DESIGN.md`, replacement repository, replacement package или replacement class;
    - новый generic `http_api_client` provider не создаётся, поскольку он дублировал бы Requests Session and `retry_runtime` без самостоятельного stable semantic owner;
    - shared Requests Session, timeout and transport retry принадлежат `retry_runtime`;
    - shared external response-schema foundation принадлежит `base_api_schema`;
    - authentication, endpoint locations, request and response contracts, accepted statuses, provider errors, quota scopes and endpoint idempotency принадлежат concrete external integration;
    - `OzonAgent` and `WbAgent` пока сохраняют current names; их возможное переименование принадлежит отдельному concrete-integration naming batch и не является скрытой частью удаления base class;
    - provider repository `submodule-base-api-agent` может быть archived only after every consumer has migrated, verified and removed its gitlink;
  - applicability owner: `project-standards:http-api-client-developer`;
  - transformation:
    - `BaseApiAgent` inheritance удаляется из `OzonAgent` and `WbAgent`;
    - generic public `get`, `post`, `put`, `patch` and `delete` methods удаляются без compatibility wrappers;
    - concrete clients expose endpoint or domain operations;
    - one integration-local private request method is allowed only for mechanics that remain semantically identical across its real call sites;
    - every endpoint owns its HTTP method, path, payload or parameters, accepted statuses, response type, provider error mapping, retry boundary and side-effect semantics;
    - each concrete client receives a dedicated Session created through `requests_session_create`;
    - Session injection, thread confinement or synchronization, close and context lifecycle have one explicit creator-side owner;
    - concrete clients do not expose their Session as a public transport escape hatch;
    - provider-specific rate-limit state remains in its concrete integration and uses the provider's real quota scope rather than hostname alone;
    - 204, provider error response, unexpected status, invalid JSON and transport failure remain distinct outcomes;
    - endpoint and retry logs use safe operation metadata and never disclose credentials, payloads, URL user information, query strings or raw external exception text;
  - approved capability wording:

```md
# http-api-client-developer

Use when code adds or changes an outbound HTTP integration, API client, endpoint request or response mapping, external error handling, provider rate limiting, or endpoint retry semantics.

Each concrete external integration owns authentication, endpoint locations, request and response contracts, accepted status codes, provider errors, quota scopes, and endpoint idempotency.

Shared Requests transport, timeout, and transport retry use `retry_runtime/DESIGN.md`. Shared external response-schema behavior uses `base_api_schema/DESIGN.md`.

A public integration client exposes endpoint or domain operations. Public generic HTTP verb methods and generic base-client inheritance are forbidden.

Each endpoint operation defines its HTTP method, path, payload or parameters, accepted statuses, response type, error mapping, retry boundary, and side-effect semantics.

Provider-specific rate limiting remains inside that integration and is keyed by the provider's real quota scope rather than hostname alone.

One Session has an explicit lifecycle owner. Credentials, payloads, URL user information, query strings, and raw external exception text are not logged.
```

  - protected consumer result:
    - block `base_api_agent` is removed from `Standard Submodule List`;
    - `base_api_agent/` tree node and explanatory bullet are removed from `Key Directory Map`;
    - projects with an outbound HTTP integration select `project-standards:http-api-client-developer` through `Required Standards`;
    - no replacement consumer submodule entry is created;
  - call-site semantic migration gate:
    - before changing one endpoint call site, record its current callable, HTTP method, path, parameters or payload, expected response shapes, accepted status behavior, retry layers, timeout, rate-limit inputs, external side effects and final error handling;
    - classify every Ozon and Wildberries `POST` independently as read-like, idempotent mutation, non-idempotent mutation, task creation, polling, buffered write or another exact endpoint operation;
    - replace each internal generic verb call with a concrete endpoint or domain method instead of moving generic verbs into another class;
    - inspect every helper that currently calls `agent.post`, `agent.put`, `agent.patch` or `agent.delete` and preserve its exact buffer ownership and success, rejection and ambiguous-failure behavior;
    - do not infer retry safety from HTTP method alone;
    - generic transport retry and higher operation retry follow `Batch 24` and do not repeat the same request through nested owners;
    - a definitive provider 429 may be retried only according to the concrete provider contract; ambiguous connect, read or response failure remains a separate retry decision;
    - Wildberries rate-limit migration records the exact documented quota owner for each endpoint and does not substitute hostname as quota identity;
    - if one client instance or credential is shared concurrently, migration proves the synchronization and Session confinement contract from actual use; otherwise no unused concurrency abstraction is added;
    - each current Session construction and ownership path is traced to its top-level creator and receives an explicit close path;
    - current divergent branches are evidence of behavior only; neither main nor the newest timestamped branch is copied mechanically as the target;
  - implementation and compatibility acceptance:
    - implement and verify `project-standards:http-api-client-developer`, `retry_runtime/DESIGN.md` and `base_api_schema/DESIGN.md` before deleting the consumer base;
    - add direct tests for every migrated concrete endpoint family covering success, provider error, unexpected status, invalid JSON, timeout, allowed retry and forbidden ambiguous retry;
    - add request-count tests proving non-idempotent Ozon and Wildberries mutations are not duplicated after ambiguous failure;
    - add rate-limit tests for the documented Wildberries quota scope, remaining burst, 429 retry delay and concurrency behavior that is actually used;
    - add a regression test proving `move_order_to_package()` sends the intended method, path and payload;
    - verify 204 produces the endpoint's declared result and is never decoded as JSON;
    - verify Session is dedicated as required, is not exposed publicly and is closed by its declared lifecycle owner on success and failure;
    - verify logs and exception text exposed to ordinary logging do not contain credentials, payloads, URL user information or query strings;
    - remove `base_api_agent` imports, inheritance, test skips, direct method tests, submodule dependency instructions and build-context references;
    - remove the `base_api_agent` gitlink and `.gitmodules` entry from `workflow-control-center`, `marketplace-tr-priority`, `marketplace-tools` and `marketplace-tr` only after that consumer's tests pass;
    - remove duplicate historical `.gitmodules` declarations in `marketplace-tr` as part of the same final-state cleanup;
    - update protected `Standard Submodule List` and `Key Directory Map` only after provider-first requirements and consumer migration pass;
    - archive `submodule-base-api-agent` only after all known consumers no longer reference it and remote verification confirms no remaining active branch consumer.

- `B25-2 historical api_agent lineage`
  - source: дополнительный consumer inventory после утверждения `B25-1`;
  - verified state:
    - `marketplace-next-co-uk` содержит stale historical gitlink `api_agent` на revision `3393406647dbbf771a3526faa1a2968eafb83d14`, но GitHub repository `submodule-api-agent` уже удалён;
    - его `ApiAgentBase` является предшественником `BaseApiAgent`: тот же commit присутствует в history `submodule-base-api-agent`, а последующий rename commit меняет `base.py` на `base_api_agent.py` и `ApiAgentBase` на `BaseApiAgent`;
    - `marketplace-next-co-uk/ozon_agent` наследуется от historical `ApiAgentBase`, поэтому это пятый фактический consumer того же retiring abstraction, хотя его path, repository и class используют прежние names;
  - transformation:
    - inventory `B25-1` трактуется как четыре consumers current-name `base_api_agent` и один consumer historical-name `api_agent`;
    - `marketplace-next-co-uk` проходит тот же endpoint-by-endpoint semantic migration без compatibility wrapper;
    - после consumer verification удаляются gitlink и `.gitmodules` entry `api_agent`;
    - удалённый repository `submodule-api-agent` не воссоздаётся и не получает replacement remote;
  - acceptance:
    - acceptance `B25-1` распространяется на `marketplace-next-co-uk`;
    - final workspace не содержит imports `api_agent`, `ApiAgentBase`, gitlink `api_agent` или параллельный historical base-client provider.

### Batch 26: ozon_seller_api

Пользователь явно утвердил этот batch 24 июля 2026 года с поправкой к owner boundary: `agent_group` и общий multi-shop configuration contract остаются в provider, поскольку они имеют одинаковую семантику во всех marketplace consumers. Ozon-specific workflow, instructions и tools принадлежат domain plugin `marketplace-agent-tools`, а не generic `project-standards`.

- `B26-1 ozon_agent -> ozon_seller_api`
  - sources:
    - block `ozon_agent` в `Standard Submodule List` обоих protected `AGENTS.md`;
    - `marketplace-tr-priority/.codex/skills/ozon-seller-api-workflow/SKILL.md`;
    - `ozon_agent/AGENTS.md` current main provider revision;
  - source state:
    - protected blocks различаются: `workflow-control-center` содержит краткое host-role описание, SHA-256 `715b8cb9f243922127767a56883e250872b94f8f99caba953d4c31b337705de0`, а `marketplace-tr-priority` содержит строгий public-wrapper usage contract, SHA-256 `4d3deff02017203e91ee315a7ed5af1b74027fe74a8a7a88545a05050f8cb68e`;
    - provider `AGENTS.md` в `workflow-control-center` и `marketplace-tr-priority` byte-identical и смешивает stable Ozon API design, generic code rules, local paths, commands и publication workflow;
  - verified consumer state:
    - пять configured consumers используют пять materially different revisions:
      - `workflow-control-center` использует `c473d6027a6ee7244261d3c84c9ab98e270f3ea9`;
      - `marketplace-tr-priority` использует remote main revision `3b0b78106d3a399e8d71389617bbcfc9d7af4c89`;
      - `marketplace-tools` использует divergent branch revision `e7302cb5b51a1ac76ed918209ac1ddca9ae9075d`;
      - `marketplace-tr` использует ancestor revision `4143826ac3002dda318a9ec55f58e26458618832`;
      - `marketplace-next-co-uk` использует older revision `b0ba2140370115d81d35ad5de4c955f3b18dedee`;
    - production host import files вне submodule насчитывают `0` в `workflow-control-center`, `36` в `marketplace-tr-priority`, `26` в `marketplace-tools`, `27` в `marketplace-tr` и `1` в `marketplace-next-co-uk`;
    - current revisions нельзя упорядочить как один safe latest pointer: `marketplace-tools` branch и current main расходятся от common ancestor, сохраняют разные endpoint families, schemas, tests и host-specific behavior;
    - public imports смешивают package-root exports, `schemas` package imports и direct internal schema-module imports;
    - current package root использует lazy `__getattr__`, а `schemas/__init__.py` использует wildcard imports;
    - current main `OzonAgentConfig` одинаково собирает несколько shops по suffixed environment groups `OzonAgentApiKey<key>`, `OzonAgentClientId<key>` и `OzonAgentGroup<key>`;
    - `agent_group` реально используется как stable shop grouping identity в `marketplace-tr-priority`, `marketplace-tools`, `marketplace-tr` и historical consumers; перенос его parser и semantics в каждый host создал бы несколько копий одного marketplace contract;
    - branch-specific `gender`, `fbo_enabled`, `fbs_stock_exclusive`, `fbs_stock_set_zero`, `fbs_stock_update`, `rfbs_stock_set_zero` и `rfbs_stock_update` не являются общим API-client contract и отсутствуют в current main;
    - current `OzonAgent` наследует retiring `BaseApiAgent`, публично раскрывает generic HTTP verbs и получает nested retry layers;
    - current buffered classes используют `add` and `flush`, но возвращают несовместимые booleans, где `True` означает success в одних operations и error в других, возвращают raw maps и могут терять результаты automatic flush;
    - некоторые buffered operations очищают payload в `finally`, включая ambiguous transport failure;
    - old provider revisions содержат Playwright access к authenticated `seller.ozon.ru`, OpenAI structured-output models и provider production module `ozon_agent_test`, хотя эти concerns не принадлежат official Seller API client;
    - `marketplace-tools` и `marketplace-tr` реально используют `send_message_with_playwright`, поэтому эта behavior должна мигрировать к host browser integration, а не удаляться;
    - current deterministic documentation mirror занимает около `4.7 MB`, содержит pinned OpenAPI snapshot и generated Markdown и используется offline naming tests;
    - `marketplace-tr-priority/.codex/skills/ozon-seller-api-workflow` сочетает полезный Ozon-specific development workflow с generic submodule publication ordering;
    - direct host calls к official `api-seller.ozon.ru` вне provider в проверенных production files не найдены;
    - authenticated browser requests к `seller.ozon.ru/api/**` являются другой external boundary и не должны автоматически считаться official Seller API bypass;
  - owner decision:
    - provider сохраняется как самостоятельный domain `Submodule`, но repository получает target name `submodule-ozon-seller-api`, package and path получают target name `ozon_seller_api`, а client class получает target name `OzonSellerApiClient`;
    - remote repository rename является отдельным publication action, но final state не сохраняет permanent mismatch между repository, package и path names;
    - `agent_group` остаётся обязательным provider-owned shop metadata field рядом с Ozon `client_id`;
    - общий suffixed multi-shop environment parsing остаётся provider-owned и не копируется в host projects;
    - existing environment prefixes `OzonAgentApiKey`, `OzonAgentClientId` и `OzonAgentGroup` сохраняются в этом batch как один deployed external configuration contract; их возможное переименование требует отдельного explicit environment migration и не выводится автоматически из internal class rename;
    - Ozon API key хранится как secret-bearing configuration value и не раскрывается через repr, errors, logs или public client fields;
    - transport timeout and retry configs используют `retry_runtime`; один общий Ozon config parser не создаёт parallel timeout or retry semantics;
    - branch-specific shop operation flags and `gender` остаются host-owned, поскольку они не имеют одинакового provider semantics во всех consumers;
    - official Ozon Seller API client не владеет authenticated Seller web UI, Playwright, OpenAI structured output, persistence, host business rules или host test doubles;
    - Ozon-specific development workflow, domain audit guidance and domain tools принадлежат `marketplace-agent-tools:ozon-seller-api-developer`;
    - generic outbound HTTP rules принадлежат `project-standards:http-api-client-developer`;
    - generic retry rules принадлежат `project-standards:python-retry-developer` and `retry_runtime/DESIGN.md`;
    - generic runtime configuration rules принадлежат `project-standards:runtime-config-developer`;
    - generic submodule publication ordering принадлежит `agent-workflows:git-commit`;
  - provider target owner: `ozon_seller_api/DESIGN.md` в repository `submodule-ozon-seller-api`;
  - domain applicability owner: `marketplace-agent-tools:ozon-seller-api-developer`;
  - transformation:
    - `OzonAgent` переименовывается в `OzonSellerApiClient`;
    - `OzonAgentConfig` разделяется по real lifecycle на `OzonSellerApiConfig`, который парсит и валидирует complete multi-shop environment groups, и `OzonSellerApiClientConfig`, который представляет одну shop configuration с `agent_group`, `client_id` и protected API key;
    - provider не создаёт host-local configuration copies; один parsed config list используется для создания clients;
    - client owns one dedicated Session created through `requests_session_create`, does not expose it, and has explicit close and context-manager lifecycle;
    - generic public HTTP verbs удаляются согласно `Batch 25`;
    - package-root public exports становятся eager and explicit;
    - public external schema types импортируются только через `ozon_seller_api.schemas`; direct imports from internal schema modules and wildcard exports are removed;
    - endpoint methods сохраняют deterministic Ozon-specific naming from endpoint path with the leading version segment removed;
    - exact upstream payload, response and field names remain at the external boundary;
    - list endpoints hide pagination and expose deterministic complete iteration;
    - stateful endpoint types use explicit owner-coupled `OzonSellerApi...Buffer` names and `add` plus `flush`;
    - buffer flush returns one endpoint-specific typed `OzonSellerApi...Result`; request-level failures raise typed exceptions, while row-level rejection and task details remain result data;
    - boolean results whose polarity varies by endpoint are removed;
    - automatic flush is allowed only when the buffer preserves and aggregates its result; final explicit flush returns the aggregate result of every automatic and final batch;
    - empty flush returns the endpoint's empty typed result;
    - success, provider error, unexpected status, invalid JSON, timeout and ambiguous transport failure remain distinct outcomes;
    - request retry, operation retry and buffer retention or release follow `Batch 24` without nested execution owners;
    - authenticated Seller web UI and Playwright methods migrate to their actual host browser owner;
    - provider production test doubles migrate to owner-local consumer tests;
    - provider docs downloader moves to `tool/ozon_seller_api_docs_download.py`;
    - deterministic upstream snapshot moves to `docs/generated/ozon-seller-api/**` and remains available offline;
    - ordinary provider tests use the pinned snapshot without network; explicit integration refresh validates current official source;
    - local skill `marketplace-tr-priority/.codex/skills/ozon-seller-api-workflow` is removed only after `marketplace-agent-tools:ozon-seller-api-developer` is installed and scenario-verified;
    - old repository, path, package, class, buffer names and compatibility aliases are not retained after coordinated consumer migration;
  - approved domain capability wording:

```md
# ozon-seller-api-developer

Use when code adds, changes, audits, or consumes an Ozon Seller API client endpoint, external schema, pagination flow, buffered operation, provider configuration, documentation snapshot, or provider submodule revision.

The canonical provider is `ozon_seller_api`, and `ozon_seller_api/DESIGN.md` owns its stable client, configuration, endpoint, schema, buffering, error, lifecycle, and documentation-snapshot contracts.

Generic outbound HTTP behavior follows `project-standards:http-api-client-developer`. Generic retry behavior follows `project-standards:python-retry-developer` and `retry_runtime/DESIGN.md`. Generic runtime configuration follows `project-standards:runtime-config-developer`. Generic submodule publication uses `agent-workflows:git-commit`.

Host code uses public endpoint or buffered-operation APIs from the provider. Direct official Seller API URLs, generic public transport verbs, internal provider module imports, caller-managed pagination, and host-local copies of the multi-shop configuration parser are forbidden.

An endpoint change is validated against the pinned OpenAPI snapshot and the current official Ozon contract. Tests cover method, path, payload, response parsing, pagination, provider failure, malformed response, timeout, allowed retry, forbidden ambiguous retry, and buffered state when applicable.

Official `api-seller.ozon.ru` operations and authenticated `seller.ozon.ru` browser flows are separate boundaries. Browser-session behavior is not moved into the official Seller API provider merely because both systems belong to Ozon.
```

  - approved provider wording:

```md
# ozon_seller_api

## Purpose

`ozon_seller_api` owns the reusable synchronous client, external schemas, multi-shop configuration, buffered endpoint operations, typed results, and deterministic documentation snapshot for the official Ozon Seller API.

It does not own host business policy, project persistence, authenticated Seller web UI behavior, Playwright automation, OpenAI structured output, or consumer test doubles.

## Public interfaces

The package-root public interface exposes `OzonSellerApiClient`, `OzonSellerApiClientConfig`, `OzonSellerApiConfig`, the supported owner-coupled `OzonSellerApi...Buffer` types, their typed `OzonSellerApi...Result` types, and the public Ozon Seller API exception types.

`ozon_seller_api.schemas` is the canonical public import surface for supported external response schemas and enums.

Exports are eager and explicit. Wildcard exports, lazy compatibility exports, internal-module consumer imports, old names, and alternate public import paths are not part of the interface.

## Multi-shop configuration

`OzonSellerApiConfig` parses complete configuration groups identified by one common suffix across:

- `OzonAgentApiKey<key>`;
- `OzonAgentClientId<key>`;
- `OzonAgentGroup<key>`.

Every discovered key must be present in all three groups. Missing, malformed, empty required, duplicate, or otherwise inconsistent groups fail before any client or Session is created. Diagnostics identify configuration keys without values.

`OzonSellerApiClientConfig` represents one validated shop configuration and contains `agent_group`, `client_id`, and a protected API key. `agent_group` is provider-owned stable shop metadata shared by marketplace consumers.

The API key is never exposed through repr, ordinary serialization, logs, errors, or public client state.

Transport timeout and retry configuration comes from `retry_runtime`; this provider does not implement another timeout parser or retry policy.

## Client lifecycle

`OzonSellerApiClient` owns one dedicated Requests Session created through `retry_runtime.requests_session_create`.

The Session is private. The client has an explicit close and context-manager lifecycle, and the top-level creator closes every created client on success and failure.

The client does not expose generic HTTP verb methods.

## Endpoint and schema contract

One public endpoint operation owns its HTTP method, relative path, payload or parameters, accepted statuses, response schema, provider error mapping, retry boundary, and side-effect semantics.

Public endpoint names map mechanically from the official endpoint path after removing the leading API version segment. Exact upstream request keys, response keys, and schema field names remain unchanged at the external boundary.

List endpoints hide pagination. Callers never manage offset, cursor, last-id, page, or equivalent provider tokens.

External response schemas inherit from the integration-local schema base over `BaseApiSchema`. Business logic does not live in external schema models.

## Buffered operations

A stateful endpoint uses one owner-coupled `OzonSellerApi...Buffer` with `add` and `flush`.

The provider owns the documented endpoint batch limit. Automatic flush is allowed only when every automatic result is retained and included in the final aggregate result.

`flush` returns one endpoint-specific typed `OzonSellerApi...Result`. An empty flush returns an empty result of that type. Request-level failure raises a typed exception; item-level rejection, counters, accepted identities, and task details remain typed result data.

Boolean results with endpoint-dependent success or error polarity are forbidden.

Payload snapshot, retry, ambiguous failure, buffer retention, release, and reconciliation follow the operation-specific contract and `retry_runtime/DESIGN.md`. One external request has exactly one retry owner.

## Error and logging contract

Transport failure, timeout, unexpected status, invalid JSON, provider error response, item-level rejection, and ambiguous mutation outcome remain distinguishable.

Errors and logs may identify the operation, safe status, safe provider code, attempt number, client id, and agent group. They never disclose the API key, payload, URL query, raw response body, or raw external exception text.

## Documentation snapshot

`tool/ozon_seller_api_docs_download.py` explicitly refreshes `docs/generated/ozon-seller-api/**` from official sources with deterministic paths and formatting.

Ordinary tests and development use the pinned snapshot without network. A live refresh or official-source verification is an explicit integration operation.

## Host integration

Host bootstrap loads environment configuration before constructing `OzonSellerApiConfig`, creates clients from the validated client configuration list, and owns their complete close lifecycle.

Host code uses only package-root endpoint and buffer types plus schema types from `ozon_seller_api.schemas`.

Host-specific shop operation flags, Seller web UI behavior, business workflows, persistence, and test doubles stay with their actual host owners.
```

  - protected consumer result:
    - `workflow-control-center` removes the unused `ozon_agent` gitlink, `.gitmodules` entry, `Standard Submodule List` block, `Key Directory Map` node and path-specific code-test exceptions after confirming no runtime consumer;
    - `marketplace-tr-priority` replaces its protected block with:

```md
- `ozon_seller_api`
  - Root path: `ozon_seller_api/`.
  - Host contract: `ozon_seller_api/DESIGN.md`.
```

    - marketplace projects with official Ozon Seller API use select both `marketplace-agent-tools:ozon-seller-api-developer` and the applicable generic `project-standards` capabilities in `Required Standards`;
    - projects without this boundary do not retain the submodule or select the domain capability for symmetry;
  - call-site semantic migration gate:
    - inventory every production import, endpoint operation, schema type, buffered operation, configuration field, identity field, error consumer, retry owner and client lifecycle before changing one consumer;
    - preserve every actually used endpoint and schema across the five historical revisions, but do not merge unused dead methods merely because they exist on one branch;
    - classify branch-only behavior as official Seller API, authenticated Seller web UI, host policy, test support or dead code before choosing its final owner;
    - retain common `agent_group` semantics and multi-shop environment parsing in provider code;
    - move only project-specific flags and behavior after proving the destination host owner and updating every use site;
    - migrate direct internal schema imports to the explicit `ozon_seller_api.schemas` surface;
    - migrate every current boolean or raw-map buffered result according to its actual caller meaning; do not globally invert booleans or wrap arbitrary maps without a typed endpoint result;
    - trace automatic flush results, final flush behavior, partial rejection, task polling, read-after-write confirmation and ambiguous failure for each buffered operation;
    - preserve used Playwright message behavior in its host browser owner before removing it from provider;
    - replace provider production test doubles with owner-local fixtures that exercise the final public API rather than preserve test-only production modules;
    - bulk rename, pointer-only upgrade, union-of-branches merge, AST-only import rewrite or mechanical environment-parser copy is forbidden;
    - each changed call site must be explainable in terms of preserved or deliberately corrected endpoint, state, identity, retry, error and lifecycle behavior;
  - implementation and compatibility acceptance:
    - verify repository, package, path, design and public names converge on `ozon_seller_api` without compatibility aliases;
    - verify WCC has no real consumer before removing its submodule and all path-specific exceptions;
    - verify complete multi-shop group discovery, deterministic ordering, required group equality, explicit empty environment preservation, malformed values and secret-safe diagnostics;
    - verify `agent_group` and `client_id` remain available to every migrated marketplace workflow with unchanged grouping behavior;
    - verify branch-specific operation flags remain effective in their actual host owners;
    - verify package-root and `schemas` public export sets are explicit, eager and contain no alternate old import surface;
    - verify every migrated endpoint's method, relative path, payload, status, schema, pagination and failure mapping against the pinned and current official contract;
    - verify client Session privacy, one Session per client and complete close behavior on success and failure;
    - verify typed buffered results, empty flush, automatic result aggregation, partial rejection, task polling and ambiguous failure state for every used buffer family;
    - verify exact request counts and absence of nested retry for representative read, polling, mutation and buffered operations;
    - verify official API errors and ordinary logs contain no credentials, payloads, query strings, raw response body or raw external exception text;
    - verify authenticated Seller web UI workflows still operate through their host browser owner;
    - verify ordinary provider tests are offline and deterministic, while explicit integration refresh reproduces the generated snapshot layout;
    - scenario-test and install `marketplace-agent-tools:ozon-seller-api-developer` before removing the local skill;
    - commit and publish the provider before each consumer gitlink update through `agent-workflows:git-commit`;
    - update each consumer only after its endpoint, configuration, workflow and applicable repository verification pass;
    - rename or archive the remote repository only as a separately authorized publication action after all consumers use the final provider.

### Batch 27: Key Directory Map ownership и instruction paths

Пользователь явно утвердил этот batch 24 июля 2026 года. `Key Directory Map` остаётся project-local владельцем concrete path bindings, но не копирует reusable semantics внешних providers. Удаляются только отсутствующие future-use placeholders и пути assets, чья provider-first миграция уже утверждена; concrete project roots и concrete submodule roots сохраняются.

- `B27-1 External Path Reference Rules`
  - sources:
    - полный `Key Directory Map` в `workflow-control-center/AGENTS.md`, 165 строк, SHA-256 `fcf0d798b9cf1051620095538e9c9a2cc47d227866e777ec1e49a1689390b6ab`;
    - полный `Key Directory Map` в `marketplace-tr-priority/AGENTS.md`, 125 строк, SHA-256 `7d00e1a76cc23f9dac636aa5298a8cfb208f0fd9ce79c5f44f6480cb7d3e94cd`;
    - карты содержат 49 byte-identical explanatory bullets, но различаются реальными product roots, submodules, documentation paths, DB-specific layout и test paths;
  - target owner: `project-standards/plugins/project-standards/skills/project-instruction-developer/references/instruction-protocol.md`;
  - transformation: semantic extension к утверждённым `Key Directory Map Rules`, которая разделяет project-local path binding и provider-owned reusable semantics без удаления project-local structure;
  - approved target wording:

```md
### External Path Reference Rules

- A project-local `Key Directory Map` is the single owner of concrete project path bindings and project-specific path semantics.
- When reusable semantics of a mapped path are owned by a required provider-qualified skill, the local explanatory bullet MUST name that exact owner and MUST NOT copy or paraphrase its contract.
- Every current concrete owner root and every project-local path template used by the project MUST remain represented in the local map.
- A generic placeholder path that is neither used by a current project entity nor explicitly reserved by stable project design MUST NOT appear only for possible future use.
- Paths internal to a `Submodule`, `Skill`, or another provider-owned entity MUST NOT be modeled by the consuming project's map unless the consuming project owns an explicit specialization for that exact path.
- Removing a provider-owned internal path from the consuming project's map does not remove or weaken the applicable provider contract.
```

- `B27-2 Protected instruction-path cleanup`
  - sources in both protected `Key Directory Map` sections:
    - `AGENTS.md`;
    - `**/AGENTS.md`;
    - `.codex/agents/<group>/`, `.codex/agents/<group>/test/`, `.codex/agents/<group>/tool/` and `.codex/agents/<role>.toml`;
    - `.codex/config.toml`;
    - `.codex/skills/<skill>/`;
    - `<submodule_root>/`, `<submodule_root>/AGENTS.md`, `<submodule_root>/**/AGENTS.md`, `<submodule_root>/model_sqlalchemy/`, `<submodule_root>/test/` and `<submodule_root>/tool/`;
  - verified current state:
    - neither protected project contains a tracked nested `Main project AGENTS.md` outside the repository root;
    - current `.codex/agents/**` and reusable `.codex/skills/**` assets already have approved provider or project-owner migrations in `Batch 7` and `Project-specific skill migration`;
    - both projects have byte-identical `.codex/config.toml`, but that file differs from the current global Codex configuration and therefore is not deleted or rewritten in this batch;
    - concrete submodule roots remain real project-local bindings, while generic submodule-internal placeholder paths describe another repository boundary;
  - transformation:
    - `AGENTS.md` path mapping remains project-local without semantic change;
    - absent `**/AGENTS.md` future-use mapping is deleted; applicable provider rules still govern a nested file if one is introduced later and require the map to be updated in that change;
    - `.codex/agents/**` map entries are deleted only after the approved `agent-workflows` provider cutover;
    - `.codex/skills/**` map entry удаляется в конкретном project только после переноса или удаления всех его skills; `workflow-control-center` удаляет entry после полного provider cutover, а `marketplace-tr-priority` сохраняет entry для project-local `trendyol-size-chart-parse`;
    - `.codex/config.toml` mapping удаляется вместе с файлами по окончательному contract `Batch 28`;
    - generic `<submodule_root>/**` placeholders are deleted from the host map; every concrete submodule root remains represented and mapped to its `Standard Submodule List` entry;
  - excluded from this batch:
    - documentation and task-artifact paths;
    - application-code and deployment paths;
    - `model_sqlalchemy/**`;
    - `test/**`, `tool/**`, `tmp/**` and `log/**`;
    - concrete standard submodule roots already governed by `Batch 15` through `Batch 26`.

### Batch 28: global Codex defaults и project config retirement

Пользователь явно утвердил исправленный batch 24 июля 2026 года. Общие instruction-size и subagent topology settings переходят в global Codex configuration. Оба protected project configs удаляются после provider cutover; conditional 32 KiB branch и per-project measurement не используются.

- `B28-1 Project Harness Configuration Rules`
  - sources:
    - byte-identical `.codex/config.toml` в `workflow-control-center` и `marketplace-tr-priority`, SHA-256 `13579510ce8a1f163ad944da8626bb5b580541e2ce8282c92cafffacc9d83f22`;
    - approved global/project configuration boundary в `DESIGN.md`;
    - current Codex configuration and `AGENTS.md` discovery contracts;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/harness-configuration.md`;
  - transformation: semantic extension, которая запрещает consumer copies общих harness defaults и сохраняет project config только для independently required project behavior;
  - approved target wording:

```md
### Project Harness Configuration Rules

- User-level harness configuration owns personal model, reasoning, feature, approval, sandbox, instruction-size, subagent-topology, and integration defaults shared across projects.
- Project-local harness configuration MAY contain only settings backed by an independent project-specific behavior requirement.
- A value that merely duplicates the current user configuration or a documented harness default without an independent project requirement is forbidden.
- Reusable workflows and domain skills MUST adapt to available harness capabilities and MUST NOT require copied consumer-local model, concurrency, or named-role configuration.
- If no project-specific setting remains, the project-local harness configuration file and its `Key Directory Map` entry MUST be deleted.
```

- `B28-2 Global Codex configuration`
  - target owner: user global Codex `config.toml`;
  - transformation:
    - set top-level `project_doc_max_bytes = 524288`;
    - preserve `max_threads=255` behavior through current canonical `[agents]` key `max_concurrent_threads_per_session = 255`;
    - move `[agents].max_depth = 3` without changing its value;
    - do not add duplicate explicit `apps`, `goals` or `multi_agent` values when current Codex defaults or existing global values already enable them;
  - semantics:
    - `project_doc_max_bytes` is an upper bound for the combined project instruction chain loaded from project root through the current working directory, not a reserved or always-consumed context allocation;
    - the 512 KiB global bound prevents instruction truncation in any governed project and eliminates per-project instruction-size config;
    - `max_concurrent_threads_per_session` caps open spawned-agent threads but does not guarantee that the active harness supplies that many physical parallel slots;
    - `max_depth=3` permits nested delegation only through the third subagent level;
- `B28-3 Protected project config retirement`
  - duplicate values removed with the file:
    - `model = "gpt-5.6-sol"`;
    - `model_reasoning_effort = "max"`;
    - `personality = "pragmatic"`;
    - `suppress_unstable_features_warning = true`;
    - `web_search = "live"`;
    - `approval_policy = "never"`;
    - `sandbox_mode = "danger-full-access"`;
    - `[features].apps = true`;
    - `[features].goals = true`;
    - `[features].multi_agent = true`;
  - migrated values:
    - `project_doc_max_bytes = 524288`;
    - `[agents].max_threads = 255` through canonical global key `max_concurrent_threads_per_session`;
    - `[agents].max_depth = 3`;
  - removed owner-local role entries:
    - `[agents."code-antipattern-audit-instrumental"]`;
    - `[agents."code-antipattern-audit-semantic"]`;
  - cutover gates:
    - global config parses and exposes the three approved values in a fresh Codex session;
    - `agent-workflows` role contracts and scenario tests pass without consumer-local named-agent config;
    - the protected project `.codex/agents/**` and `.codex/skills/**` migrations reach their approved owners;
    - each protected `.codex/config.toml` and its `Key Directory Map` entry are then deleted without a compatibility copy.

### Batch 29: documentation и task-artifact paths

Пользователь явно утвердил этот batch 24 июля 2026 года. Он определяет final owner paths для tracked documentation и task artifacts двух protected projects, но не разрешает semantic shortening или decomposition смешанных документов. Path cutover выполняется без forwarding documents и только после обновления всех repository-local references.

- `B29-1 Source inventory`
  - `workflow-control-center`:
    - 17 tracked files under `doc/**` and `pattern/**`;
    - sorted path-and-content manifest SHA-256 `9d539da91591e889e087dd3a5c9d19c429123a06dbf15a72618de3d75e2084f2`;
  - `marketplace-tr-priority`:
    - 13 tracked files under `doc/**` and `pattern/**`;
    - sorted path-and-content manifest SHA-256 `29d33aa1aa2fd7e9360d3351890caa32e15236089a336e21060b149e2c81fb3b`;
  - target owners:
    - stable architecture and domain contracts: project-local `DESIGN.md` and `design/**` under `project-standards:project-documentation-developer`;
    - maintained operational and reference documentation: project-local `docs/**` under `project-standards:project-documentation-developer`;
    - active or unclassified task pairs: ignored project-local `.spec/**` under `agent-workflows:goal-brainstorm`;
    - reusable script workflow pattern: `project-standards:python-developer` according to `B2-3`.
- `B29-2 workflow-control-center path mapping`
  - target mapping:
    - `doc/design.md` -> `DESIGN.md`;
    - `doc/design/backend.md` -> `design/backend.md`;
    - `doc/design/data-storage.md` -> `design/data-storage.md`;
    - `doc/design/frontend.md` -> `design/frontend.md`;
    - `doc/design/observability.md` -> `design/observability.md`;
    - `doc/design/persistence.md` -> `design/persistence.md`;
    - `doc/design/workflow-runtime.md` -> `design/workflow-runtime.md`;
    - `doc/design/zitadel.md` -> `design/zitadel.md`;
    - `doc/domain.md` -> `design/domain.md`;
    - `doc/local_kubernetes.md` -> `docs/local_kubernetes.md`;
    - `doc/script_catalog.md` -> `docs/script_catalog.md`;
    - `pattern/script_workflow_owner.md` -> provider owner in `B2-3`, followed by deletion of the empty local `pattern/`;
  - transformation: retained owner paths migrate with complete semantic preservation except mechanical self-reference and repository-reference updates required by the new paths; the three explicit deletion entries are governed only by `Batch 31`;
  - targetless retirement:
    - `doc/draft/ozon-product-card-fill-skill.md` удаляется без переноса по окончательному contract `Batch 34`.
- `B29-3 workflow-control-center task pairs`
  - `ui-error-observability` lifecycle:
    - persistent goal state explicitly reports `status=complete` for the objective owned by `doc/goal/2026-07-22-ui-error-observability.md`;
    - before deletion, semantic review confirms that every durable architecture, operation, security and verification requirement is present in the retained stable design, documentation, code or infrastructure owners named by that completed pair;
    - `doc/spec/2026-07-22-ui-error-observability.md` and `doc/goal/2026-07-22-ui-error-observability.md` are then deleted without `.spec/` copies or forwarding documents;
  - `vpn-config-network-proxy` lifecycle:
    - repository history and current implementation prove substantial work but do not prove a current persistent lifecycle status;
    - the pair therefore remains `unclassified` and is preserved without interpreting partial implementation as completion;
  - retained target mapping:
    - `doc/spec/2026-07-22-vpn-config-network-proxy.md` -> `.spec/2026-07-22-vpn-config-network-proxy-spec.md`;
    - `doc/goal/2026-07-22-vpn-config-network-proxy.md` -> `.spec/2026-07-22-vpn-config-network-proxy-goal.md`;
  - transformation:
    - copy the retained VPN content into the ignored target pair before removing its tracked source files;
    - update mutual and stable-owner references to final paths without changing outcome, constraints or verification semantics;
    - verify ignored VPN targets directly by path and semantic reread because `git status` cannot prove their presence;
    - do not delete the VPN pair until a later explicit lifecycle decision classifies it as completed or abandoned and stable requirements have been extracted.
- `B29-4 marketplace-tr-priority path mapping`
  - target mapping:
    - `doc/design/index.md` -> `DESIGN.md`;
    - `doc/design/brand_size_chart_load.md` -> deletion without target under `Batch 31`;
    - `doc/design/brand_size_chart_workflow_sync.md` -> deletion without target under `Batch 31`;
    - `doc/design/codex_exec_workflow.md` -> deletion without target under `Batch 31`;
    - `doc/design/ozon_product_archive_sync.md` -> `design/ozon_product_archive_sync.md`;
    - `doc/design/site_parsing_runtime.md` -> `design/site_parsing_runtime.md`;
    - `doc/design/trendyol_cart_sync.md` -> `design/trendyol_cart_sync.md`;
    - `doc/design/trendyol_offer_product_parse.md` -> `design/trendyol_offer_product_parse.md`;
    - `doc/ozon_sales_model_v1.md` -> `design/ozon_sales_model_v1.md`;
    - `doc/price_currency_policy.md` -> `design/price_currency_policy.md`;
    - `doc/script_catalog.md` -> `docs/script_catalog.md`;
    - `doc/trendyol-offer-list-json-api.md` -> `docs/trendyol-offer-list-json-api.md`;
    - `pattern/script_workflow_owner.md` -> provider owner in `B2-3`, followed by deletion of the empty local `pattern/`;
  - transformation: owner-path migration with complete semantic preservation except mechanical self-reference and repository-reference updates required by the new paths;
  - protected mixed content:
    - target path for `DESIGN.md` does not authorize semantic shortening;
    - the three superseded brand-size-chart and Codex-exec source documents remain protected until the explicitly approved semantic retirement in `Batch 31`, after which no forwarding or replacement design documents are created.
- `B29-5 Cutover gates`
  - create and semantically verify every retained target before deleting its source; the three targetless retirement sources follow `B31-6` instead;
  - update every affected plain root-relative reference in protected `AGENTS.md`, code, tests, task artifacts and neighboring documents;
  - verify direct runtime behavior when code reads a moved documentation path;
  - preserve task pairs in ignored `.spec/` before tracked source removal;
  - do not leave forwarding documents, compatibility paths or simultaneously active old and new documentation owners;
  - remove old `doc/**` and `pattern/**` directories only after every approved migration and targetless retirement has reached its final owner or verified deletion state.

### Batch 30: marketplace-tr-priority root design

Пользователь явно утвердил этот batch 24 июля 2026 года. `marketplace-tr-priority/doc/design/index.md` не переносится механически целиком: root design становится архитектурным маршрутизатором, stable Trendyol-to-Ozon product semantics переходят в один thematic design owner, durable engineering rules переходят в protected project `AGENTS.md`, а уже заменённый provider contract и одна доказанная устаревшая scope-фраза не сохраняются как параллельные владельцы.

- `B30-1 Source inventory`
  - source: полный `marketplace-tr-priority/doc/design/index.md`, 95 строк, SHA-256 `f52c8854ceef2251e8d6855d1fa5bf45cc3acdbc58adf7324a61b971b40ee214`;
  - reviewed section ranges:
    - lines `3-10`, SHA-256 `da5d97cd1e975cfd2d3201a90bc5b53a2222ecc47c4d860ff07c0f395c7f6184`;
    - lines `12-17`, SHA-256 `d35d69b35c84dc0cf9d02ad8e07ffa7cb1ad7d864dfa7647064d66b`;
    - lines `19-22`, SHA-256 `8f5952b153465a48797d099e05becfad396525af41813acc39d75dfed9fe2422`;
    - lines `24-27`, SHA-256 `ec0368d304303ae108b816cd4040d7d03b5d2e114fccf2e7854ce71db450a241`;
    - lines `29-38`, SHA-256 `2ae7d006514da7712734259c636786b09ef85ab724dd756758b16c2f27ff7bf0`;
    - lines `40-47`, SHA-256 `0efcc68152bc9fcdb048a6c727a525650f4b69aaa29bba228b2ec60c0e359c51`;
    - lines `49-56`, SHA-256 `ec4b84918c31cd80f9161158535d982b6670766ced4ebffa32428c3b6482e176`;
    - lines `58-63`, SHA-256 `a32dc419f034f2adffa2c90b2de0101c96df88e5903ecd21804ecae04469292a`;
    - lines `65-88`, SHA-256 `63e2415c64da5dd6b9533cbf05221c50154b3dcd7a001e4ae662143989ac055c`;
    - lines `90-95`, SHA-256 `99717026125a44db737c9d707b0a42deb9b138d991e14ef243f5ca9b1b17c410`.
- `B30-2 Root DESIGN.md`
  - target owner: `marketplace-tr-priority/DESIGN.md`;
  - source ranges:
    - lines `3-10`;
    - routing part of lines `90-91`;
  - transformation:
    - root file becomes the architecture entrypoint and complete router for the final thematic documents approved in `B29-4`;
    - existing owner statements for site-parsing runtime and Ozon archive synchronization are preserved with final root-relative paths;
    - owner statements for brand-size-chart loading, brand-size-chart workflow sync and `codex exec` workflow runtime are omitted under the approved retirement in `Batch 31`;
    - one owner entry for `design/trendyol_to_ozon_product.md` is added;
    - other final thematic documents from `B29-4` are routed from the same root instead of remaining undiscoverable sibling files;
    - implementation-level algorithms, path-owner instructions and duplicated action lists do not remain in the root router.
- `B30-3 Trendyol-to-Ozon product design`
  - target owner: `marketplace-tr-priority/design/trendyol_to_ozon_product.md`;
  - source ranges:
    - lines `12-17`;
    - lines `29-63`;
    - project-scoped Ozon mutation outcome contract from lines `65-66`;
    - domain invariant part of line `70`;
    - lines `72-88`;
  - preserved stable semantics:
    - boundaries of `parsed_offer_meta`, `parsed_offer_ozon_score` and normalized product tables;
    - Trendyol size identity and normalized `size_value`, including canonical `''` for a product without a size;
    - Ozon size mapping rules for empty single variants, historical size rows, `child_height_ru` and `bra_ru`;
    - valid Ozon article identity, `delme_` treatment and hard failure for other invalid articles;
    - distinction between `not_sellable` and other Ozon product errors, including the `--update-error-only` scope;
    - project requirement that a mutating Ozon operation wait for its task or confirm its resulting state and surface unconfirmed state as failure;
    - seller-barcode validation before write planning as a product-domain invariant;
    - package-quantity Ozon type mapping and its materialization order;
    - product-level and group-level shop bindings, selection precedence and transaction-before-import boundary;
    - Ozon attribute `8292` representation of `product_group_key`;
    - behavior of group-key repair, group-shop-binding repair and mismatch report, and product-shop-binding repair;
  - specialized payload and archive algorithms remain in their existing final thematic owners and are referenced instead of copied.
- `B30-4 Protected engineering rules`
  - target owner: protected `marketplace-tr-priority/AGENTS.md`;
  - source ranges:
    - lines `19-22`;
    - lines `24-27`;
    - implementation-owner and duplication-ban part of line `70`;
  - transformation:
    - the repeated technical-settings rule becomes a project-local engineering contract: one integration or domain-function owner supplies one validated `*Config` model and one reusable `*_cli_args_add(parser, scope=...)` interface, and root scripts do not duplicate its `parser.add_argument(...)` blocks;
    - the current `requests_retry` owner sentence is replaced by the already approved `retry_runtime/DESIGN.md` and `project-standards:python-retry-developer` owners instead of preserving the retiring name;
    - `lib/trendyol/product_image_classification/` remains the concrete owner of `ProductImageClassificationConfig` and `product_image_classification_cli_args_add(...)`;
    - the local-stage rule becomes the project-local `Project Apply Mode Contract`: local DB materialization and repair required to build the current run plan behave identically with and without `--apply`, while `--apply` enables external mutations unless one script design defines a narrower exception;
    - `lib/barcode/seller.py` remains the sole implementation owner of seller-barcode validation, consumers call `seller_barcode_issue_get(...)` before write planning, and consumer-local regex, prefix, length or equivalent validation copies remain forbidden;
    - concrete path ownership is represented in the protected `Key Directory Map`; non-structural usage obligations reference those mapped owners without duplicating path semantics.
- `B30-5 Superseded and conflicting source semantics`
  - source line `68` is not copied:
    - current project code still uses old endpoint-specific result methods such as `.flush_error_list()`, `.flush_success_get()` and `.item_error_list()`;
    - `Batch 26` already replaces this interface through the provider-owned `add`, `flush` and endpoint-specific typed `Result` contract;
    - preserving line `68` would create a second incompatible owner and is forbidden;
  - source lines `93-95` are normalized through the specialized archive owner:
    - the exclusivity requirement that `ozon_product_archive_sync.py` is the only stock, archive and unarchive synchronization workflow moves to `design/ozon_product_archive_sync.md`;
    - the root statement that the script performs only stock, archive and unarchive is stale because current code, `docs/script_catalog.md` and the specialized design also include seller-barcode add;
    - the specialized design remains canonical and states the complete current scope: seller-barcode add plus stock, archive and unarchive, without product-card upload;
    - the stale shortened action list is deleted instead of retained as a conflicting compatibility statement.
- `B30-6 Cutover gates`
  - build a complete source-line-to-target ledger for lines `1-95` and verify every semantic unit against `B30-2` through `B30-5`;
  - create and semantically verify `DESIGN.md` and `design/trendyol_to_ozon_product.md` before deleting `doc/design/index.md`;
  - update all references to the final root and thematic paths without forwarding documents;
  - verify one owner for technical settings, apply mode, seller-barcode implementation, Ozon provider buffering and every product-domain contract;
  - compare the archive action scope against current code, the specialized design and `docs/script_catalog.md`;
  - documentation and instruction changes in this batch do not authorize runtime behavior changes;
  - handoff verification is semantic reread, repository-reference validation, Markdown structural checks and `git diff --check`; root pytest is not required solely for this documentation and instruction migration.

### Batch 31: retirement старого marketplace-tr-priority brand-size-chart contour

Пользователь явно утвердил этот batch 24 июля 2026 года и отдельно потребовал не удалить действующие таблицы и алгоритмы, которые сопоставляют размеры товаров Trendyol с размерами Ozon. Этот batch удаляет только точный старый brand-level execution contour; совпадение слов `brand`, `size` или `chart` в имени не является основанием для удаления.

- `B31-1 Source inventory и доказанная изоляция`
  - deletion candidate set содержит 69 tracked files;
  - sorted path-and-content SHA-256 manifest: `b293d336901b2346db4d8f4bb68fc0189f545dd7489804223e07bfd1afdfbd31`;
  - ключевые source hashes:
    - `.codex/skills/brand-size-chart-load/SKILL.md`: `0cf1ac7541aecb160c8348c0c39bd0f85dae17ead25b8b6ddc95548e26089064`;
    - `.codex/skills/codex-exec-workflow/SKILL.md`: `6ebf4e446b22a9276b8e36b0276adefec73b481fe35e613e89ec14da6894a844`;
    - `doc/design/brand_size_chart_load.md`: `d71c993f2dd980cc285e0e1524d207be62db0f8d500af6cb044c0e84fc34c22c`;
    - `doc/design/brand_size_chart_workflow_sync.md`: `f737679a098a81a65ddd304ec2e281ebda950d5a5821d59a25c09124f4650252`;
    - `doc/design/codex_exec_workflow.md`: `0c008e261aada60f0ac5e3596dc2504397bdf8ea9a9fa7c1cc7820e883b28d7b`;
    - `model_sqlalchemy/marketplace_tr_priority/parsed_brand_size_chart.py`: `ff658899c61c33c0da30c05cc6b81f94f7c907639f162aa2cda0bf43049347c7`;
    - `model_sqlalchemy/marketplace_tr_priority/parsed_brand_size_chart_candidate.py`: `f81326f4aa7fb481d4b48b7d3d5f27be9a32cca5da97174f5f8ecc200a39a682`;
  - current tracked-code search proves `ParsedBrandSizeChart` and `ParsedBrandSizeChartCandidate` are read or written only by `lib/parsed_brand_size_chart/load/updater.py` and exported by their model package; no current product runtime reader consumes either table;
  - current Trendyol-to-Ozon size mapping instead reads `ParsedOfferProductSizeChartImage` and `ParsedOfferProductSizeChartParse`, resolves rows through `OzonSizeReferenceStore`, writes `ParsedOfferProductOzonSize`, and exposes that output to dimension building, product upload and archive synchronization.
- `B31-2 Final ownership и no-successor decision`
  - `workflow-control-center` owns the product control plane for `WorkflowSource`, tested `WorkflowSourceVersion`, `Workflow`, `WorkflowRun`, execution lifecycle and Data acceptance;
  - separate project `brand-size-chart` owns the workflow domain runtime, source discovery, validation, canonical selection, publisher tests and declared result/workspace datasets;
  - `marketplace-tr-priority` does not retain or recreate a second brand-size-chart launcher, validator, loader, WCC sync wrapper, local canonical brand-chart store or skill facade;
  - `codex-exec-workflow` and `brand-size-chart-load` have no target skill, compatibility alias, App Server `skills/list` runtime lookup, provider asset symlink or forwarding document;
  - generic `agent-workflows` subagent transport and batch mechanics remain because other approved agent workflows consume them; they are not production dependencies of `brand-size-chart`.
- `B31-3 Exact deletion allowlist`
  - delete `.codex/skills/brand-size-chart-load/**` and `.codex/skills/codex-exec-workflow/**`;
  - delete `doc/design/brand_size_chart_load.md`, `doc/design/brand_size_chart_workflow_sync.md` and `doc/design/codex_exec_workflow.md` without target documents;
  - delete all tracked files under `lib/parsed_brand_size_chart/**`;
  - delete root entrypoints `parsed_brand_size_chart_launch.py`, `parsed_brand_size_chart_load.py`, `parsed_brand_size_chart_validate.py` and `parsed_brand_size_chart_workflow_sync.py`;
  - delete all tracked files under `script/parsed_brand_size_chart_launch/**`, `script/parsed_brand_size_chart_load/**`, `script/parsed_brand_size_chart_validate/**` and `script/parsed_brand_size_chart_workflow_sync/**`;
  - delete `tool/parsed_brand_size_chart_worker_result_summarize.py` and `tool/parsed_brand_size_chart_worker_task_prepare.py`;
  - delete ORM declarations `model_sqlalchemy/marketplace_tr_priority/parsed_brand_size_chart.py` and `model_sqlalchemy/marketplace_tr_priority/parsed_brand_size_chart_candidate.py`, then remove only their imports and exports from package registries;
  - delete tests whose only subject is this retired contour:
    - `test/code/test_brand_size_chart_skill.py`;
    - `test/code/test_codex_exec_workflow_contract.py`;
    - `test/integration/test_brand_size_chart_models_integration.py`;
    - `test/test_brand_size_chart_contract.py`;
    - `test/test_brand_size_chart_models.py`;
    - `test/test_parsed_brand_size_chart_codex_exec_validator.py`;
    - `test/test_parsed_brand_size_chart_launch.py`;
    - `test/test_parsed_brand_size_chart_load.py`;
    - `test/test_parsed_brand_size_chart_validate.py`;
    - `test/test_parsed_brand_size_chart_workflow_sync.py`;
    - `test/test_workflow_control_center_brand_size_chart_client.py`;
  - update the current `doc/design/index.md` and `doc/script_catalog.md`, or their already-created final owners from `Batch 29`, plus package registries and repository-local references; these dependent edits are not additional deletion authority;
  - do not create a migration, compatibility model or physical database-table drop; existing database rows and physical tables are outside this repository-only retirement and production DB writes remain separately gated.
- `B31-4 Protected Trendyol-to-Ozon size pipeline`
  - this batch MUST NOT delete, rename, rewrite or semantically narrow:
    - `ParsedOfferProductImage`;
    - `ParsedOfferProductImageLink`;
    - `ParsedOfferProductSizeChartImage`;
    - `ParsedOfferProductSizeChartParse`;
    - `ParsedOfferProductOzonSize`;
    - `lib/ozon/size_reference.py`;
    - `ozon_size_reference/**`;
    - root entrypoint and owner package `parsed_offer_product_ozon_size_build.py` and `script/parsed_offer_product_ozon_size_build/**`;
    - root entrypoint and owner package `parsed_offer_product_ozon_dimension_build.py` and `script/parsed_offer_product_ozon_dimension_build/**`;
    - product upload and Ozon archive consumers of `ParsedOfferProductOzonSize`;
    - `test/test_brand_size_chart_reference.py`, `test/test_ozon_size_reference.py`, `test/test_parsed_offer_product_ozon_dimension_build.py` and `test/test_parsed_offer_product_ozon_size_build.py`;
  - `trendyol-size-chart-parse` remains a separate project-local marketplace workflow that writes `ParsedOfferProductSizeChartImage` and `ParsedOfferProductSizeChartParse`; it does not move to `marketplace-agent-tools`, and its instructions, executable tools, payload contract, curated data and stored-data behavior remain owned by `marketplace-tr-priority`;
  - path- or substring-based deletion using `brand`, `size`, `chart`, `parsed_*size*` or similar broad matching is forbidden.
- `B31-5 Future brand dataset integration boundary`
  - deleting the retired local contour does not claim that current MTP product mapping consumes the new `brand-size-chart` result dataset;
  - if brand-level workflow results must later participate in Trendyol-to-Ozon product-size mapping, a separate approved product change must define an explicit WCC Data or Athena read boundary, brand and size-group identity, precedence against product-image charts, refresh and failure semantics, and direct behavior coverage;
  - dead local ORM models, copied workflow output or a retained manual loader MUST NOT serve as an undocumented bridge for that future integration.
- `B31-6 Cutover gates`
  - before deletion, recompute the tracked reference graph for every deletion candidate and stop if any current runtime consumer exists outside the approved old contour, package registries, tests, design index or script catalog;
  - verify the exact 69-file candidate inventory against the recorded manifest and review any drift semantically before changing the allowlist;
  - after deletion, prove there are no tracked references to `brand-size-chart-load`, `codex-exec-workflow`, `ParsedBrandSizeChart`, `ParsedBrandSizeChartCandidate`, `parsed_brand_size_chart_launch`, `parsed_brand_size_chart_load`, `parsed_brand_size_chart_validate` or `parsed_brand_size_chart_workflow_sync`;
  - prove every protected ORM model remains registered and every protected source-loader, mapper and downstream consumer import remains resolvable;
  - run the applicable targeted tests for `test/test_brand_size_chart_reference.py`, `test/test_ozon_size_reference.py`, `test/test_parsed_offer_product_ozon_dimension_build.py` and `test/test_parsed_offer_product_ozon_size_build.py`;
  - because Python code and code-contract tests change, run the protected project Black scope, remaining `test/code/**`, ordinary handoff suite `pytest --ignore=test/code -q` and every narrower applicable direct verification contract;
  - run `brand-size-chart` publisher tests and its applicable compile/import checks to prove the canonical workflow project remains independently valid;
  - perform no production database write, table drop, data migration or WCC runtime mutation during this retirement;
  - handoff must report the deleted exact set, preserved size pipeline evidence, all verification commands and any physical database tables intentionally left untouched.

### Batch 32: явное сохранение project-local trendyol-size-chart-parse

Пользователь явно утвердил этот batch 24 июля 2026 года и отклонил automatic consumer-count criterion для переноса skills. Пользователь принимает отдельное решение по каждому source skill; это решение не требует заданного числа consumers или отдельного reuse обоснования.

- `B32-1 Ownership decision`
  - полный `marketplace-tr-priority/.codex/skills/trendyol-size-chart-parse/**` остаётся project-local `Skill`;
  - `marketplace-agent-tools` не получает `trendyol-size-chart-parse`, replacement skill, forwarding facade, copied references или copied tools;
  - current project-specific instructions, `agents/openai.yaml`, manual rules, payload contract, executable tools, tracked curated table JSON и ignored scratch images не перемещаются, не нормализуются и не удаляются этой standardization goal;
  - обнаруженная возможность отдельно нормализовать 50 legacy curated JSON из URL-hash identity в content-hash identity является самостоятельной product-local задачей и не входит в этот batch.
- `B32-2 Shared dependency boundary`
  - reusable sequential-batch и subagent mechanics остаются утверждённым shared owner `agent-workflows`;
  - `agent-workflows:sequential-batch` является provider-qualified public `Skill` и stable dependency project-local `trendyol-size-chart-parse`; consumer не ссылается на plugin cache path;
  - после provider cutover project-local skill заменяет только ссылки на удаляемые consumer-local `.codex/agents/sequential-batch/**` и `.codex/agents/subagent-transport/**`; его собственная domain procedure, tool commands, result schema, apply gates и stored-data behavior не меняются.
- `B32-3 Instruction-model amendments`
  - `B3-3 Domain Plugin Ownership Rules` использует explicit user-approved source-to-target decision вместо consumer count, potential reuse или agent inference;
  - `B7-1 Skill` допускает provider `Skill` и явно сохранённый пользователем project-specific `Skill`, причём project-local identity действует только внутри owning project;
  - отсутствие project-local copies reusable provider skills не означает запрет явно сохранённых project-specific skills.
- `B32-4 Cutover gates`
  - provider `agent-workflows:sequential-batch` должен быть установлен, обнаружен в fresh session и scenario-verified до удаления consumer-local generic agent assets;
  - current tracked inventory `marketplace-tr-priority/.codex/skills/trendyol-size-chart-parse/**` и ignored scratch image inventory проверяются до и после generic dependency cutover; drift вне точечных instruction references запрещён;
  - direct skill scenario подтверждает прежние prepare, validation, dry-run apply, apply, status, manual-only image inspection, payload validation и DB selection semantics;
  - никакие production DB writes, curated-data transformation, project-runtime relocation или `marketplace-agent-tools` changes не выполняются этим batch.

### Batch 33: retirement Python discovery package

Пользователь явно утвердил этот batch 24 июля 2026 года после проверки фактической функциональности и workspace-wide consumers.

- `B33-1 Verified current behavior`
  - `workflow_container_tools/project.py` только просматривает непосредственные соседние каталоги относительно parent `tools_path`, выбирает каталоги с одновременным наличием `workflow.yaml` и `versions.yaml` и возвращает их в стабильном порядке;
  - `workflow_container_tools/cli.py` предоставляет только read-only команду `python -m workflow_container_tools.cli list` с параметром `--tools-path` и печатает `<name>\t<path>`;
  - package не читает содержимое marker files, не валидирует workflow, не запускает workflow, не выполняет audit и не обращается к API или БД.
- `B33-2 Consumer evidence`
  - workspace-wide поиск по текущим 20 checkout не нашёл внешних imports `workflow_container_tools`, вызовов module CLI или consumers `WorkflowContainerProjectFinder`;
  - все code consumers находятся внутри самого package и его tests, а все operator references находятся в его README;
  - Codex plugin installation не устанавливает этот Python package.
- `B33-3 Final target`
  - удалить `workflow_container_tools/**`, его CLI и project-discovery behavior без replacement package, compatibility command или переноса в `workflow-container-agent-tools`;
  - удалить package-specific behavior tests `test/test_cli.py` и `test/test_project.py`;
  - удалить Python wheel package declaration, optional CLI description, commands и tree entries из package metadata, README и project instructions;
  - сохранить только Python environment или test-runner metadata, которое реально требуется оставшимся provider tests; package-identity assertions преобразовать в проверки final marketplace и plugin owners либо удалить, если они не проверяют оставшийся public contract;
  - regenerated dependency metadata не должно содержать удалённую local package distribution как installable product.
- `B33-4 Verification`
  - target repository не содержит `workflow_container_tools`, module CLI command, `WorkflowContainerProjectFinder`, optional local discovery contract или wheel package declaration;
  - marketplace и все три installable plugins проходят structural validation и applicable provider tests после удаления package;
  - README, `AGENTS.md`, `DESIGN.md`, package metadata и dependency metadata описывают только final plugin-provider surface;
  - удаление не создаёт нового discovery wrapper или другого replacement interface.

### Batch 34: retirement нереализованного ozon-product-card-fill draft

Пользователь явно утвердил этот batch 24 июля 2026 года при условии, что задача до сих пор не реализована. Current-state проверка доказала выполнение этого условия.

- `B34-1 Verified current state`
  - `workflow-control-center/doc/draft/ozon-product-card-fill-skill.md` добавлен одним commit `22ea8d2db996206cbdad268e6b1491a72b7a65c5` от 14 мая 2026 года и не имеет последующих изменений;
  - workspace-wide поиск не нашёл ссылок на `ozon-product-card-fill`, `ozon_product_card_fill` или `ozon-product-type-schema` вне самого draft;
  - `workflow-control-center` не содержит соответствующих skills, tools, runtime package, generated schemas, product artifacts или partial implementation.
- `B34-2 Final target`
  - удалить `workflow-control-center/doc/draft/ozon-product-card-fill-skill.md` без replacement document, `.spec/` pair, skill, plugin asset или нового workflow-container project;
  - не переносить Ozon workflow, DB-specific preparation, schema-authoring proposal или superseded consumer-local sequential-batch paths из этого draft в stable owners;
  - удалить `workflow-control-center/doc/draft/`, если после targetless retirement в нём не остаётся других текущих artifacts;
  - не изменять application, database, Ozon integration или workflow runtime behavior.
- `B34-3 Verification`
  - target file и все repository references на него отсутствуют;
  - финальный workspace не содержит созданных этой миграцией `ozon-product-card-fill`, `ozon-product-type-schema` или эквивалентных replacement assets;
  - `workflow-control-center` проходит applicable documentation/reference checks и semantic review подтверждает, что удалено только нереализованное task proposal.

### Batch 35: сохранение project-local Ozon skills

Пользователь явно утвердил этот batch 24 июля 2026 года после проверки реальных tools, data owners и production consumers.

- `B35-1 ozon-attribute-map`
  - полный `marketplace-tr-priority/.codex/skills/ozon-attribute-map/**` остаётся project-local `Skill`, включая `SKILL.md`, `agents/openai.yaml`, deterministic tool и owner-local tests;
  - project data roots `ozon_attribute_map/**`, production owner `script/parsed_offer_product_ozon_attribute_build/**`, его root entrypoint, tests и script-catalog contract сохраняются;
  - skill остаётся привязанным к project-local Trendyol-to-Ozon mapping JSON, Product DB/reference data и current repository workflow и не переносится в `marketplace-agent-tools`.
- `B35-2 ozon-data-contract`
  - полный `marketplace-tr-priority/.codex/skills/ozon-data-contract/**` остаётся project-local `Skill`;
  - contract продолжает применяться к `ozon_data_parse.py`, `script/ozon_data_parse/**`, project ORM metadata, authenticated local profile boundary и `_test` DB verification;
  - skill не преобразуется в generic provider standard и не копируется в `marketplace-agent-tools`.
- `B35-3 ozon-dimension-reference`
  - полный `marketplace-tr-priority/.codex/skills/ozon-dimension-reference/**` остаётся project-local `Skill`, включая `SKILL.md`, `agents/openai.yaml`, deterministic tool и owner-local tests;
  - project data roots `ozon_dimension_reference/**` и `ozon_size_reference/**`, production owner `script/parsed_offer_product_ozon_dimension_build/**`, его root entrypoint, tests и script-catalog contract сохраняются;
  - skill остаётся владельцем project-local reference maintenance workflow и не переносится в `marketplace-agent-tools`.
- `B35-4 Allowed migration changes`
  - разрешены только необходимые изменения ссылок на переименованные project documentation paths, provider-qualified common standards и generic agent owners, фактически применимые к каждому skill;
  - domain workflow, command surface, data paths, tool behavior, payload semantics, validation rules и production consumers не изменяются этой standardization goal;
  - `marketplace-agent-tools` не получает copies, replacements, forwarding skills или extracted fragments этих трёх skills.
- `B35-5 Verification`
  - tracked inventories трёх skill roots и их project data roots сравниваются до и после migration; drift вне разрешённых instruction references запрещён;
  - owner-local tool tests, непосредственно связанные production behavior tests и applicable full `marketplace-tr-priority` handoff suite проходят;
  - direct commands `--help` и безопасные deterministic validation/status paths остаются работоспособными;
  - semantic review подтверждает, что каждый skill остаётся discoverable, project-local и согласованным со своим current code/data owner.

### Batch 36: полный retirement compose-codex-app-server

Пользователь явно подтвердил полный необратимый scope 24 июля 2026 года. GitHub repository и local checkout уже удалены; этот batch содержит только оставшийся exact Docker cleanup.

- `B36-1 Verified remaining state`
  - container `codex-app-server` остановлен четыре месяца;
  - image `compose-codex-app-server-codex-app-server:latest`, volume `compose-codex-app-server_codex-home-runtime` и empty network `compose-codex-app-server_default` существуют;
  - workspace-wide exact-reference search не нашёл active consumers repository или его прежнего HTTP API.
- `B36-2 Exact Docker cleanup`
  - удалить остановленный container `codex-app-server`;
  - удалить image `compose-codex-app-server-codex-app-server:latest`; read-only inventory доказал, что единственным использующим его container является `codex-app-server`;
  - удалить volume `compose-codex-app-server_codex-home-runtime`;
  - удалить пустую local bridge network `compose-codex-app-server_default`;
  - не удалять другие containers, images, volumes, networks или Compose projects по name-prefix approximation.
- `B36-3 No-successor boundary`
  - не воссоздавать local checkout, GitHub repository, forwarding remote, mirror или branch;
  - не переносить прежние skills, application code, contracts, Compose configuration или runtime data в `agent-plugins`, `project-standards`, `workflow-control-center` либо другой repository;
  - общие standards и workflows создаются только из остальных утверждённых source contracts и не используют retired repository как normative source;
  - project standardization применяется к 18 existing canonical repositories и создаваемому отдельно `project-standards`, то есть к 19 final canonical repositories.
- `B36-4 Verification`
  - `docker ps -a`, image inventory, volume inventory и network inventory не содержат exact artifacts из `B36-2`;
  - workspace-wide exact-reference search по canonical repositories не находит active dependency на `compose-codex-app-server`, `codex-app-server`, его прежние HTTP endpoints или port `8765`;
  - semantic review подтверждает отсутствие replacement и удаления Docker artifacts за пределами exact allowlist.

### Batch 37: retirement template-bin и разрыв template lineage

Пользователь явно утвердил retirement `template-bin` 24 июля 2026 года. Текущие runtime files остаются у каждого consumer как полностью самостоятельные project-local files; общий template owner, update remote и synchronization contract удаляются.

- `B37-1 Verified current state`
  - `template-bin` содержит только семь tracked files под `bin/**`, включая `bin/readme.txt` с командами добавления remote, `fetch` и merge `template-bin/main --allow-unrelated-histories`;
  - template не является `Submodule`, package dependency или runtime service: consumers получили его commits через обычный Git merge и уже владеют tracked copies;
  - шесть current consumers содержат `bin/**`:
    - `compose-milvus`;
    - `compose-mysql`;
    - `marketplace-next-co-uk`;
    - `marketplace-parser`;
    - `marketplace-tools`;
    - `marketplace-tr`;
  - current `bin/**` уже разделён на три materially different tree states:
    - `marketplace-next-co-uk` совпадает с current `template-bin`, tree `919a7a4e666c59eea59648e840cce7617d8d1845`;
    - `compose-milvus` и `compose-mysql` используют tree `14c29e3fd891e5dafbcc80cd47038a70d2a5d6e5`;
    - `marketplace-parser`, `marketplace-tools` и `marketplace-tr` используют tree `6a8945d21c58080bffa04b94bc035339d5eca372`;
  - configured local remote `template-bin` существует только в `marketplace-next-co-uk` и `marketplace-tools`; остальные consumers сохраняют только tracked update instructions.
- `B37-2 Final ownership`
  - каждый consumer становится единственным владельцем собственного `bin/**` и может изменять его независимо от других projects;
  - совпадающее содержимое в нескольких consumers не создаёт shared owner или synchronization obligation: после cutover назначение и lifecycle каждого файла определяет только его project;
  - новый shared repository, plugin, submodule, generated copy mechanism или replacement template не создаётся;
  - `template-bin` исключается из project standardization scope и не получает `AGENTS.md`, `DESIGN.md` или другую migration work.
- `B37-3 Consumer detachment`
  - до изменений сохранить для каждого consumer sorted manifest tracked `bin/**` с file modes и SHA-256;
  - удалить obsolete `bin/readme.txt`, поскольку его единственная функция — восстановление retiring template relation;
  - сохранить все остальные current `bin/**` побайтно и с прежними executable modes;
  - удалить configured local remote `template-bin` из `marketplace-next-co-uk` и `marketplace-tools`;
  - удалить active tracked documentation, configuration и automation references, которые предлагают fetch, merge или иное обновление из `template-bin`;
  - не переписывать Git history: historical merge commits, commit messages и unreachable local reflog entries не являются active dependency и не удаляются этим batch.
- `B37-4 Repository retirement`
  - retirement выполняется только после успешной detachment и verification всех шести consumers;
  - local checkout `template-bin` удаляется без переноса содержимого после доказательства, что consumer manifests сохранены;
  - agent удаляет exact GitHub repository `antonov-andrey-org/template-bin` после consumer cutover в рамках явно разрешённого этой specification lifecycle action и только при подтверждённом scope `delete_repo`;
  - не создавать archive, mirror, forwarding remote, replacement branch или другое место продолжения template lineage.
- `B37-5 Verification`
  - каждый consumer manifest после cutover отличается от baseline только отсутствием `bin/readme.txt`;
  - `bash -n` проходит для каждого сохранённого shell script, а executable modes совпадают с baseline;
  - configured remotes всех canonical repositories не содержат `template-bin`;
  - tracked workspace search не находит active `template-bin` URL, fetch, merge или update instruction;
  - applicable verification каждого изменённого consumer проходит без выполнения destructive deployment scripts;
  - final canonical inventory содержит 18 existing repositories и новый `project-standards`, всего 19 repositories.

### Batch 38: cleanup уже удалённых workflow-source-codex и submodule-anthropic-agent

Пользователь сообщил 24 июля 2026 года, что GitHub repositories `antonov-andrey-org/workflow-source-codex` и `antonov-andrey-org/submodule-anthropic-agent` уже удалены. Authenticated GitHub lookup подтвердил отсутствие обоих repositories. Повторная remote deletion запрещена.

- `B38-1 workflow-source-codex final state`
  - до удаления repository содержал только `LICENSE` и один initial commit, не имел runnable workflow source, local checkout, gitlink, tracked workspace reference, open pull request или open issue;
  - repository не является migration source и не получает replacement, archive, mirror или forwarding remote;
  - implementation не создаёт cleanup artifacts только для фиксации уже завершённого deletion;
  - final verification подтверждает отсутствие active reference и не пытается воссоздать repository.
- `B38-2 submodule-anthropic-agent remaining state`
  - `marketplace-next-co-uk` содержит tracked gitlink path `anthropic_agent` mode `160000` на commit `5daa9056cb8265d9b144559d27ee776125b86c2c`;
  - `.gitmodules` содержит section `submodule-anthropic-agent` с удалённым remote `git@github.com:antonov-andrey-org/submodule-anthropic-agent.git`;
  - local submodule configuration и local Git metadata всё ещё существуют;
  - tracked consumer search вне gitlink не находит import, construction или call site `AnthropicAgent`;
  - самостоятельная integration `marketplace-next-co-uk/textgrad_engine/anthropic.py` использует upstream Python package `anthropic`, не зависит от deleted submodule и является protected unrelated code.
- `B38-3 Exact anthropic cleanup`
  - удалить tracked gitlink `anthropic_agent` и соответствующую `.gitmodules` section;
  - удалить только owner-local submodule configuration, worktree и Git metadata этого exact gitlink;
  - не удалять или изменять `textgrad_engine/anthropic.py`, upstream `anthropic` dependency, environment contract либо другой Anthropic-related application code;
  - не создавать replacement package, copied wrapper, compatibility import или новый repository;
  - не выполнять повторный GitHub deletion request.
- `B38-4 Verification`
  - `git ls-files --stage`, `.gitmodules`, local Git config, `git submodule status` и local submodule metadata не содержат `anthropic_agent` или `submodule-anthropic-agent`;
  - tracked workspace search не находит deleted repository URL, `AnthropicAgent` consumer или stale gitlink reference;
  - existing `textgrad_engine` Anthropic behavior и applicable `marketplace-next-co-uk` verification проходят без deleted submodule;
  - authenticated GitHub lookup подтверждает отсутствие обоих repositories;
  - semantic review подтверждает no-recreate behavior и отсутствие изменений за пределами exact cleanup.

### Batch 39: atomic rename cutover workflow-container-tools

Пользователь явно утвердил 24 июля 2026 года, что rename repository должен включать замену всех active Git URLs в других projects и переименование всех directories с его live local copies. Этот batch является обязательной частью rename `antonov-andrey/workflow-container-tools` в `antonov-andrey/agent-plugins`, а не последующим cleanup.

- `B39-1 Verified current identity inventory`
  - canonical current GitHub identity и `origin`: `antonov-andrey/workflow-container-tools` и `git@github.com:antonov-andrey/workflow-container-tools.git`;
  - primary checkout: `/home/andrey/Projects/workflow-container-tools`;
  - current installable-plugin source directory: `plugins/workflow-container-tools`;
  - current Codex configuration использует keys `workflow-container-tools@workflow-container-tools` и `marketplaces.workflow-container-tools` с source `/home/andrey/Projects/workflow-container-tools`;
  - current installed cache использует marketplace directory `/home/andrey/.codex/plugins/cache/workflow-container-tools` и plugin directory `/home/andrey/.codex/plugins/cache/workflow-container-tools/workflow-container-tools`;
  - current repository не имеет linked worktrees;
  - непосредственно перед mutation inventory повторяется для всех canonical projects, global harness configuration и plugin roots, чтобы обнаружить появившиеся после specification active URLs, worktrees, symlinks или local copies.
- `B39-2 Canonical target identities`
  - GitHub repository: `antonov-andrey/agent-plugins`;
  - canonical SSH URL: `git@github.com:antonov-andrey/agent-plugins.git`;
  - primary checkout и любой repository or marketplace-source copy используют directory name `agent-plugins`, включая target primary checkout `/home/andrey/Projects/agent-plugins`;
  - current installable plugin становится `workflow-container-agent-tools`; его source и installed-cache directories используют `workflow-container-agent-tools`, а не repository name;
  - new sibling plugins используют собственные identifiers `agent-workflows` и `marketplace-agent-tools`;
  - marketplace configuration key становится `agent-plugins`, а enabled-plugin keys используют `agent-workflows@agent-plugins`, `marketplace-agent-tools@agent-plugins` и `workflow-container-agent-tools@agent-plugins`;
  - installed cache root становится `/home/andrey/.codex/plugins/cache/agent-plugins`, а его plugin child directories используют target plugin identifiers, включая `/home/andrey/.codex/plugins/cache/agent-plugins/workflow-container-agent-tools`.
- `B39-3 Active reference migration`
  - после hosting rename заменить old SSH, HTTPS и equivalent Git URLs во всех configured remotes, tracked `.gitmodules`, dependency or provider manifests, CI, deployment configuration, project instructions, documentation и global harness/plugin configuration;
  - переименовать primary checkout и все обнаруженные под `/home/andrey` live local checkout, worktree, marketplace-source, plugin-source, installed-plugin и cache directories согласно `B39-2`, включая copies за пределами final canonical repository inventory;
  - обновить symlink targets, configured source paths, plugin manifests, marketplace metadata и active project references на target identities;
  - не оставлять old URL, old configured remote, old filesystem owner path, old marketplace key, old installable-plugin key или discovery result как fallback, alias или compatibility bridge;
  - не переписывать commits, tags, reflogs, completed logs, immutable task history и другие historical evidence, где old identity описывает прошлое состояние.
- `B39-4 Provider-safe sequencing`
  - до GitHub rename target provider tree с тремя plugins проходит local structural, automated и scenario verification;
  - GitHub repository rename, primary checkout rename, `origin` update, provider metadata update, global Codex marketplace/plugin reconfiguration и reinstall выполняются как один resumable cutover;
  - old installed cache удаляется только после успешной установки target marketplace и fresh-session discovery всех трёх plugins;
  - сбой до доказанного target discovery сохраняет исходные provider files и допускает продолжение cutover, но не разрешает steady state с одновременно active old и new identities.
- `B39-5 Verification`
  - authenticated GitHub lookup находит `antonov-andrey/agent-plugins`, published default branch совпадает с local `HEAD`, а canonical `origin` равен `git@github.com:antonov-andrey/agent-plugins.git`;
  - `git worktree list`, workspace directory inventory, symlink inventory, Codex marketplace/plugin config и plugin cache inventory не содержат live local copy под old identity;
  - active workspace search по canonical projects и global harness/plugin configuration не находит old SSH, HTTPS или equivalent Git URL;
  - fresh Codex session обнаруживает `agent-workflows@agent-plugins`, `marketplace-agent-tools@agent-plugins` и `workflow-container-agent-tools@agent-plugins`, но не обнаруживает old marketplace или plugin;
  - явно исторические Git objects, reflogs, completed logs и immutable task history исключаются из active-reference assertion и не изменяются;
  - semantic review подтверждает, что repository copies названы `agent-plugins`, installable-plugin copies названы своими plugin identifiers и ни один active consumer не зависит от old identity.

### Batch 40: terminal completion audit/fix fixed point

Пользователь явно утвердил этот batch 25 июля 2026 года и отдельно запретил создавать дополнительные completion ledger, evidence document или completion report. Root cause незавершённого выполнения этой goal находился в асимметрии lifecycle: `goal-brainstorm` требовал semantic review до activation, но после implementation не требовал повторного полного аудита всего completion contract с нуля. Поэтому закрытые checklist items и успешные tests могли ошибочно считаться достаточными, даже когда часть specification оставалась нереализованной.

- `B40-1 Stable owner`
  - `agent-plugins/DESIGN.md` владеет общей terminal audit/fix semantics для persistent goals;
  - `plugins/agent-workflows/skills/goal-brainstorm/references/specification-contract.md` владеет точным task-artifact lifecycle contract;
  - `plugins/agent-workflows/skills/goal-brainstorm/SKILL.md` требует включить этот contract в создаваемую goal и persistent objective;
  - текущая сохранённая goal/spec pair применяет тот же contract к собственному завершению.
- `B40-2 Fixed-point semantics`
  - после предполагаемого завершения выполняется новый полный audit всей goal, paired specification, referenced stable source contracts и текущего repository и external state;
  - audit не ограничивается implementation plan, changed files, закрытыми checklist items, прошедшими tests или предыдущими findings;
  - любая незавершённая, противоречивая, устаревшая или непроверенная находка сохраняет goal active;
  - все находки исправляются, затронутая verification повторяется, после чего следующий полный audit начинается с нуля;
  - завершение разрешено только после нового полного аудита без находок после последнего исправления и при продолжающемся успехе required verification;
  - fixed iteration limit, partial recheck и completion из-за дороговизны оставшейся работы запрещены; реальный внешний blocker использует harness goal-blocking contract.
- `B40-3 No extra proof artifacts`
  - terminal cycle работает непосредственно по текущим source contracts и system state;
  - отдельные completion ledger, evidence document, completion report и другие обязательные proof artifacts не создаются;
  - существующий protected instruction source-to-target ledger остаётся частью этой implementation specification и не является terminal completion evidence artifact.
- `B40-4 Acceptance`
  - provider plugin и skill validators проходят после изменения;
  - semantic reread подтверждает одинаковую fixed-point semantics в `DESIGN.md`, skill reference, skill workflow и этой pair;
  - текущая goal не переводится в `complete` до выполнения этого цикла на всём заявленном scope.

### Batch 41: closure protected instruction ledger

Пользователь явно потребовал 25 июля 2026 года сначала закрыть полный protected instruction ledger, затем последовательно выполнить `Batch 24`, `Batch 25` и `Batch 26`. Этот batch является последним approved review batch для `workflow-control-center/AGENTS.md` и `marketplace-tr-priority/AGENTS.md`. Он разрешает только перечисленные ниже source-to-target transformations и не разрешает потерю, ослабление или project-specific обобщение остальных contracts.

- `B41-1 Shared Python code contract`
  - sources в обоих protected files: `General Code Rules`, `non-Legacy Python code Formatting And Docstring Rules`, все subsections `non-Legacy non-test Python code Rules`, `Code Refactoring Rules`, `Naming Rules` и `non-Legacy non-test Python code outside Submodule Rules`;
  - target owner: `project-standards/plugins/project-standards/skills/python-developer/references/python-code.md` и тематические references, на которые он явно ссылается;
  - transformation: перенос полного объединённого набора правил без смыслового сокращения; byte-identical rules получают одного владельца, а существующие project-specific additions обрабатываются только по отдельным entries ниже;
  - WCC rule про settings, attributes, parameters, fields и configuration knobs включается в reusable contract;
  - MTP `External Payload Field Contract` переходит по `B41-2`, а не теряется внутри Python core;
  - WCC `request_header_map`/`response_header_map` rule остаётся project-local частью `Product API Rules`, поскольку он явно привязан к `ApiRequestLog`;
  - final protected files не сохраняют вторую consumer-local copy перенесённых generic Python rules.
- `B41-2 External payload contract`
  - source: MTP subsection `External Payload Field Contract`;
  - target owner: `project-standards/plugins/project-standards/skills/http-api-client-developer/references/http-api-client.md`;
  - transformation: перенос reusable external-boundary semantics без ослабления: один semantic value имеет один canonical source field/path; multi-shape extraction разрешена только явно задокументированным stable design contract с точным field/path set, причиной, order и failure behavior; при неопределённости contract выводится из real payload evidence;
  - protected consumer result: MTP выбирает `project-standards:http-api-client-developer` и удаляет local duplicate.
- `B41-3 Python CLI contract`
  - sources: общие subsections обоих protected files под `Entrypoint And CLI Rules`;
  - target owner: `project-standards/plugins/project-standards/skills/python-cli-developer/references/cli.md`;
  - transformation: перенос полного общего CLI, root-entrypoint, config, test-mode и direct-verification contract без смыслового сокращения;
  - generic часть MTP `Interactive CLI Prompt Contract` о dedicated prompt owner, допустимых ответах, default и reprompt semantics переносится в provider;
  - exact MTP path `lib/cli/prompt.py` и запрет direct `prompt_toolkit` imports вне этого owner остаются project-local overlay;
  - exact selection `config_argparse`, concrete `docs/script_catalog.md` contents и project-specific safe commands остаются в project overlay или existing submodule contract, а не становятся обязательными для unrelated consumers.
- `B41-4 Pytest contract`
  - sources: `Test Rules` обоих protected files;
  - target owner: `project-standards/plugins/project-standards/skills/pytest-developer/references/test.md`;
  - transformation: перенос полного объединённого reusable contract без смыслового сокращения, включая owner-local placement, root discovery, Submodule standalone/root execution, `test/code/**` boundary, support artifact placement, semantic-instruction assertion ban и required changed-behavior coverage;
  - project-specific suite commands, WCC UI browser order, exclusions, opt-in integrations и direct handoff order остаются в protected `Evidence And Verification Rules`.
- `B41-5 SQLAlchemy and database contract`
  - sources: общие и согласуемые части `Database Rules` обоих protected files, включая production-write boundary, migrations, ORM ownership, ORM rules, sessions, transactions и test DB seeding;
  - target owner: `project-standards/plugins/project-standards/skills/sqlalchemy-developer/references/sqlalchemy.md` вместе с existing `model-sqlalchemy.md` и `project-foundation/references/temporal-data.md`;
  - transformation: перенос полного общего contract без смыслового сокращения; расширения, которые применимы только к одному protected project, не навязываются второму;
  - canonical project API names, migration layout and command, ORM-first constraints, validated field semantics и shared test-seeding integrity сохраняются;
  - WCC-only Kubernetes migration execution and image rules, WCC-only automatic `_test` fixture-source rule и PostgreSQL/ZITADEL specializations остаются project-local;
  - WCC mutable product-state/append-only lifecycle с microsecond precision и MTP refreshable-snapshot lifecycle с его current precision являются конфликтующими project-specific contracts и остаются в соответствующих protected files;
  - provider явно запрещает заменять lifecycle одного project lifecycle другого только из-за совпадающих column names;
  - final protected files не сохраняют consumer-local copy только перенесённой reusable части contract.
- `B41-6 Submodule Python contract`
  - sources: `Submodule Code Rules` обоих protected files;
  - target owner: `project-standards/plugins/project-standards/skills/submodule-developer/references/submodule-model.md`;
  - transformation: перенос без смыслового сокращения запрета project-specific identifiers и direct-script portability delta;
  - concrete submodule host mappings and project-local specializations остаются в `Standard Submodule List`.
- `B41-7 Tool utility contract`
  - sources: byte-equivalent `Tool Utility Ownership Contract` обоих protected files;
  - target owner: `project-standards/plugins/project-standards/skills/project-foundation/references/repository-model.md`;
  - transformation: перенос полного reusable owner-local extraction, shared implementation, reuse и dependency-boundary contract без смыслового сокращения;
  - `Tool Scope Resolution And Verification` остаётся project-local, поскольку он определяет concrete `tool/python_code_scope_resolve.py` и local direct execution;
  - WCC `Local Kubernetes State Contract` удаляется как duplicate более полного `project-standards:kubernetes-developer` contract, который уже выбран в `Required Standards`.
- `B41-7a Main project shared boundaries`
  - sources: byte-identical `Import Boundary Contract` и `Project-Table Bootstrap Contract` обоих protected files;
  - target owners: import dependency boundary переходит в `project-standards:project-foundation/references/repository-model.md`, а SQLAlchemy readiness boundary — в `project-standards:sqlalchemy-developer/references/sqlalchemy.md`;
  - transformation: перенос без смыслового сокращения;
  - concrete shared-code placement, script-family roots and parser selection остаются project-local.
- `B41-8 Local contracts retained`
  - оба protected files сохраняют `Required Standards`, applicable local terms, concrete `Standard Submodule List`, `Key Directory Map`, `Allowed Dependency Matrix`, project-specific `Evidence And Verification Rules`, runtime versions, commands и owner paths;
  - WCC сохраняет `Worker script`, `base_worker`, backend/UI/deploy structure, `Main Project Code Rules`, `Product API Rules`, Kubernetes/UI handoff order, WCC-specific header field semantics, Kubernetes migration specialization и mutable/append-only table lifecycle;
  - MTP сохраняет `Project Engineering Rules`, seller barcode and apply-mode contracts, `Main Project Code Rules`, exact interactive prompt owner path, refreshable-snapshot table lifecycle и все marketplace-specific behavior;
  - расходящиеся project structure и verification rules не унифицируются этим batch;
  - Table Of Contents каждого protected file обновляется только в соответствии с фактически оставшимися local sections.
- `B41-9 Provider-first cutover and acceptance`
  - provider references и skills сначала проходят structural validation, semantic reread и provider tests;
  - затем protected files удаляют только approved duplicate sections и продолжают fail-closed выбирать все применимые provider skills;
  - exact source-to-target reread подтверждает, что каждое удалённое normative statement присутствует у одного final provider owner либо явно сохранено project-local этим batch;
  - final semantic comparison подтверждает отсутствие нового ослабления, compatibility copy, generated standard prose и project-specific rule leakage;
  - protected project applicable verification выполняется после cutover.

## Design и documentation migration

Проект со stable architecture получает корневой `DESIGN.md`. Малый проект хранит design непосредственно там; большой использует `DESIGN.md` как архитектурную точку входа и тематические `design/*.md`.

Содержимое `doc/design.md`, `doc/design/**` и других смешанных документов классифицируется семантически:

- stable architecture и domain contracts переходят в `DESIGN.md` или `design/**`;
- operations и usage переходят в `docs/**`;
- durable engineering instructions переходят в `AGENTS.md`;
- task-specific requirements переходят только в активную `.spec/` pair;
- завершённые task history и дубли удаляются после подтверждения, что устойчивые требования имеют другого владельца.

Переименование не оставляет forwarding documents, compatibility links или одновременно активные старые и новые documentation paths.

## Task artifact contract

Каждый canonical repository явно игнорирует `/.spec/`. Active persistent task хранит одну пару:

```text
.spec/YYYY-MM-DD-<semantic-name>-spec.md
.spec/YYYY-MM-DD-<semantic-name>-goal.md
```

Формат, owner boundaries, direct-owner и dedicated modes, activation matrix, semantic review и lifecycle определены `goal-brainstorm` reference. Completed или abandoned pair удаляется; active, blocked, paused или unclassified pair не удаляется автоматически.

Multi-repository task хранит пару только в coordinating repository. Vendor-specific state внутри `.spec/` запрещён.

По прямому требованию пользователя от 24 июля 2026 года текущая `.spec/2026-07-23-project-standardization-{spec,goal}.md` pair сохраняется после завершения этой goal и является явным исключением из общего удаления completed pair.

## Cutover, failure и recovery

Provider-first доступность является обязательным инвариантом. Consumer copy не удаляется, пока требуемые plugins из `agent-plugins` и `project-standards` не прошли structural validation, automated and scenario tests, installation и fresh-session discovery.

Неуспешная provider validation оставляет consumer assets неизменными и допускает повторную provider correction. После доказанного provider cutover consumer удаляет старую copy полностью; compatibility aliases и долгоживущий dual-provider режим запрещены. Missing required provider после cutover является fail-closed состоянием и запрещает project mutation.

Каждый canonical repository изменяется как самостоятельная проверяемая единица с сохранением unrelated user changes. Ошибка project verification исправляется в том же repository до признания его migrated. Отказ одного repository не откатывает уже проверенный provider и не разрешает объявить весь workspace завершённым.

Удаление старого tracked task artifact допустимо только после semantic review его содержимого. Неизвестный active status блокирует автоматическое удаление этого artifact, но не блокирует read-only inventory остальных проектов.

## Verification design

### Provider и plugin

Marketplace manifests, plugin manifests и каждый skill в `agent-plugins` и `project-standards` проходят применимые structural validators. Automated tests покрывают marketplace split, workspace discovery, standard selection, required-provider failure, project overlay preservation, check mode, missing metadata, secondary checkout handling и отказ от hardcoded workspace paths.

Scenario tests каждого перенесённого workflow покрывают success path, основной failure path и критические прежние сценарии обоих source repositories.

Fresh Codex session после reinstall должен видеть `agent-workflows@agent-plugins`, `marketplace-agent-tools@agent-plugins`, `workflow-container-agent-tools@agent-plugins` и `project-standards@project-standards`. Старый marketplace source, plugin identifier `workflow-container-tools` и consumer-local reusable copies не остаются active providers.

Полный rename acceptance выполняет `Batch 39`: old repository URL и old live local owner paths отсутствуют во всех active remotes, manifests, configuration, project references, marketplace sources, plugin sources, worktrees, symlinks и caches; repository и installable-plugin paths различаются согласно их canonical identities.

Verification подтверждает полный `Batch 33` retirement: `workflow_container_tools`, module CLI, package-specific tests, wheel declaration и operator references отсутствуют, а provider validation продолжает проходить без replacement discovery interface.

Scenario verification `code-antipattern-audit` подтверждает instrumental и semantic role contracts без project-local или user-global named-agent TOML, включая declared scope, двухпроходную проверку, разделение checker-grounded и independent semantic review, report artifact и handoff. Scenario verification sequential-batch workflow подтверждает обработку только назначенного batch, ограничения output scope и сохранение parent ownership над assignment, validation, database writes и apply gates. Финальный provider и workspace inventory подтверждает отсутствие `code_antipattern_audit_instrumental.toml`, `code_antipattern_audit_semantic.toml` и `sequential_batch_worker.toml` в target provider и consumer projects.

### Project instructions и design

Workspace check подтверждает для каждого canonical repository:

- наличие корневого `AGENTS.md`;
- корректные `Required Standards`, полный выбор всех применимых к фактическому project scope capability skills, доказанную user authorization для каждого исключения, доступность каждого названного skill, runtime declaration, owner paths, project commands и verification contract;
- отсутствие generated copies standard prose;
- отсутствие лишнего project-local common skill, generic agent tree и duplicated common harness config;
- наличие `DESIGN.md` только при реальном stable design и корректную классификацию child documents;
- отсутствие целевых старых `doc/design*`, tracked `doc/spec/**`, tracked `doc/goal/**` и legacy ExecPlan artifacts;
- успешный `git check-ignore .spec/example.md`;
- отсутствие tracked или stale completed `.spec` artifacts, кроме явно сохраняемой текущей project-standardization pair.

Instruction and design correctness проверяется semantic audit, а не pytest assertions по prose или file presence.

Для protected instruction migrations semantic audit дополняет, но не заменяет approved source-to-target ledger. Verification подтверждает:

- покрытие каждого исходного нормативного правила одним final owner либо отдельным явным approval на его удаление;
- отсутствие преобразований, которых нет в approved review batch;
- сохранение path scope и precedence после переноса в nested `AGENTS.md`;
- отсутствие переноса project-specific structure, command, security или product rule в generic provider;
- наличие fail-closed ссылки из project `AGENTS.md` на каждый selected capability skill;
- отсутствие выбора одной из расходящихся project semantics без отдельного approval;
- совпадение final instruction set с каждым approved batch независимо от изменения общего количества строк.

### Project behavior

Каждый repository выполняет собственные applicable formatting, targeted и full verification contracts после migration. Если перемещается executable tool или меняется harness-facing command, direct behavior test подтверждает прежний observable outcome и основной failure path.

UI, runtime, infrastructure и production behavior не считается изменённым только из-за documentation/tooling migration. Любое обнаруженное фактическое behavior change требует соответствующей owner-specific verification либо исключения этого изменения из данной миграции.

Для каждого затронутого `Legacy` consumer final changed-file inventory и semantic diff подтверждают отсутствие изменений `load_dotenv()` и `config_env` calls, dotenv file order и override semantics, environment keys, application bootstrap и зависимых `config_env`, `config_logging` и `config_sqlalchemy` gitlinks или call sites ради provider convergence.

### Workspace completion

Финальный read-only inventory охватывает 18 existing repositories и новый `project-standards`, всего 19 canonical repositories, и не находит common consumer skill copies, duplicated generic agent assets, одинаковые project-local common configs, obsolete documentation paths или stale completed task pairs, кроме явно сохраняемой текущей project-standardization pair.

Все изменённые repositories имеют clean applicable verification. Итоговый semantic review подтверждает единственного владельца каждого общего и project-specific требования, отсутствие compatibility bridges и соответствие `DESIGN.md`.

После предполагаемого завершения implementation и verification начинается terminal completion audit/fix cycle из `plugins/agent-workflows/skills/goal-brainstorm/references/specification-contract.md`. Каждый audit заново охватывает всю goal, эту specification, referenced stable source contracts и фактическое repository и external state; он не ограничивается plan, изменёнными файлами, закрытыми checklist items, прошедшими tests или предыдущими findings. Любая незавершённая, противоречивая, устаревшая или непроверенная находка сохраняет goal active: все находки исправляются, затронутая verification повторяется, после чего следующий полный audit начинается с нуля. Завершение разрешено только после нового полного аудита без находок после последнего исправления и при продолжающемся успехе всей required verification. Этот цикл не создаёт отдельный completion ledger, evidence document, completion report или другой proof artifact.
