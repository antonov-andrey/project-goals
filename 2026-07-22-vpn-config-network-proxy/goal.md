# `VpnConfig` И Универсальный `Network Proxy`

## Outcome

Полностью реализовать согласованное конечное состояние из `.spec/2026-07-22-vpn-config-network-proxy-spec.md`: разделить `browser-runtime` и `vpn-runtime`, добавить Product resources `VpnConfig` и `VpnConfigVersion` и предоставить любому workflow единый schema-driven `network_proxy` capability. Платформа должна построить exact name-to-URL map для совокупности всех proxy names из полного `input.json`, а каждый consumer — использовать exact name только из своей конкретной input-настройки без автоматического выбора или распределения. `brand-size-chart` Version `0.7.1` должен использовать этот общий контракт как первый реальный UI и runtime acceptance workflow.

## Source Contracts

- `.spec/2026-07-22-vpn-config-network-proxy-spec.md`: полный координирующий контракт scope, cutover, реализации и приёмки.
- `design/domain.md`: `Владение И Видимость`, `Имена И Метки`, `VpnConfig`, `VpnConfigVersion`, `Workflow`, `WorkflowRun`, `Отношения И Пути` и `Snapshot И Versioning`.
- `design/backend.md`: `Стандартный Механизм Ресурса`, `Управление И Selectors`, `Workflow Resources`, `VPN Resources` и `Проверки`.
- `design/frontend.md`: `Граница Management И Selector`, `Стандартные Компоненты`, `Product Resources` и `Проверки`.
- `design/persistence.md`: `Стандартные Поля`, `Workflow Persistence`, `Lifecycle` и `Разрушающий Cutover`.
- `design/data-storage.md`: `AWS Development Account`, `Secret Data`, `Workflow Data И Checkpoints` и `Проверки`.
- `design/workflow-runtime.md`: `Контракт Source`, `Import И Build`, `Post-Build Validation`, `Run Creation И Identity`, `Replacement Execution`, `Runtime Capabilities`, `Маршрутизация Proxy Из Input`, `VPN Gateway И Slots`, `Переключение Active VPN Version`, `Failure И Cleanup` и `Проверки`.
- `docs/local_kubernetes.md`: `Локальное Состояние`, `Обычный Рабочий Процесс`, `Проверка Состояния` и `Диагностика`.
- `AGENTS.md`: `Evidence And Verification Rules`, `Product API Rules`, `Database Schema Migration Rules` и `Table Lifecycle Rules`.
- `workflow-container-contract` — `README.md`: `Workflow Source Interface`, `Network Proxy Input` и `Runtime Environment and Provenance`.
- `workflow-container-runtime` — `DESIGN.md`: `Scope`, `Dependency Boundary` и `Network Proxy Boundary`.
- `agent-plugins` — `plugins/workflow-container-agent-tools/skills/workflow-container-developer/references/workflow-container-authoring.md`: `2.1. Владельцы ответственности`, `2.4. Публичный вход и форма настроек`, `2.6. Платформенный интерфейс, сборка и проверка`, `7.1. Контейнер, секреты и writeback`, `7.3. Browser runtime` и `8. Проверка реализации`.
- `browser-runtime` — `DESIGN.md`: `Browser And Proxy Boundary`, `Profile Lifecycle`, `Process And Security Boundary` и `Verification Contract`.
- `vpn-runtime` — `DESIGN.md`: `Protocol Boundary`, `Gateway Boundary`, `Prepared Activation And Fencing`, `Validation Boundary`, `Security Boundary` и `Verification Contract`.
- `brand-size-chart` — `DESIGN.md`: `Platform Source Contract`, `Public Input`, `Runtime Tree`, `Persisted Step Inputs` и `Verification` Version `0.7.1`.

## Constraints

Изменения в `workflow-control-center`, `workflow-container-contract`, `workflow-container-runtime`, плагине `workflow-container-agent-tools`, `browser-runtime`, `vpn-runtime` и `brand-size-chart` образуют одну цель и должны завершиться одним согласованным конечным состоянием. Платформа владеет recursive extraction, validation, materialization и exact name-to-URL map, но не выбирает proxy за consumer и не вводит routing state; конкретный source явно задаёт каждое используемое name в своём полном input contract. Cutover выполняется до production с пересозданием Product DB, local Kubernetes state и test rows без DB migrations, dual read/write, aliases, compatibility packages или сохранения старого combined runtime. Production AWS resources и Kubernetes cluster не изменяются; acceptance использует только утверждённый AWS development account, локальный Kubernetes и обычный AWS Secret Data flow. Существующие пользовательские изменения вне этой цели должны быть сохранены.

## Verification

Выполнить все обязательства раздела `Verification` в `.spec/2026-07-22-vpn-config-network-proxy-spec.md` и разделов проверки перечисленных owner contracts. Применить test и handoff contracts из каждого применимого `AGENTS.md`, включая target behavior tests, exact image/container checks, local Kubernetes orchestration, обязательный порядок UI tests/build/apply/browser suite/manual real-browser verification и real acceptance с несколькими VPN roots и явно настроенными consumers. Перед завершением семантически перечитать реализацию и весь набор source contracts как одно целое и подтвердить отсутствие старого combined runtime, automatic proxy selection/distribution, compatibility bridge, raw secret exposure, отдельного Workflow VPN binding, overlapping workflow writers и расхождений lifecycle, failure или recovery semantics.
