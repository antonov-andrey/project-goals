# `VpnConfig` И Универсальный `Network Proxy`

## Назначение

Эта спецификация владеет согласованным cutover нескольких репозиториев от объединённого `browser_vpn_runtime` к отдельным `browser-runtime` и `vpn-runtime`, внедрением Product entities `VpnConfig` и `VpnConfigVersion` и сквозной приёмкой input-driven platform contract `network-proxy-name`. Устойчивая доменная, storage, API/UI и runtime-семантика принадлежит соответствующим документам `design/*.md` в `workflow-control-center` и `DESIGN.md` в соседних runtime-репозиториях; этот документ не создаёт их вторые определения.

## Проверенное Исходное Состояние

До cutover `workflow-control-center` хранит и материализует source-declared `browser_vpn_runtime` Version 2 с `is_vpn_enabled` и OpenVPN secret binding. Один соседний репозиторий содержит browser router, OpenVPN gateway и SOCKS5 в одном package/image family. `brand-size-chart` объявляет combined capability в `workflow.yaml` и не имеет публичного proxy selector field. Product entities VPN, active Version, publication, shared connection-slot queue и live Version rotation отсутствуют.

GitHub repository объединённого runtime уже переименован в `browser-runtime`, локальный checkout приведён к этому имени, а новый `vpn-runtime` создан отдельно. На момент спецификации production code внутри `browser-runtime` ещё сохраняет старую combined реализацию, а `vpn-runtime` содержит только утверждённые owner contracts.

## Требуемый Результат

Пользователь управляет собственными `VpnConfig` и immutable `VpnConfigVersion` в Product UI, а администратор может публиковать пригодный root для read-only использования другими пользователями. Любой `WorkflowSource` может объявить одно или несколько явных proxy-reference fields непосредственно в полном `input.schema.json`. Платформа извлекает все сохранённые stable names, атомарно резервирует provider slots, запускает отдельные run-local VPN gateways, передаёт image safe SOCKS map и поддерживает live active-Version rotation без одновременного выполнения двух workflow writers.

Browser automation является только одним consumer этой карты. Workflow script, HTTP client или другая библиотека читает exact stable name из своей конкретной настройки полного `input.json` и получает тот же endpoint без Playwright-specific API. VPN остаётся optional: отсутствие marked values не создаёт gateway и не меняет direct egress.

## Scope И Non-Goals

В scope входят Product domain/persistence/API/UI VPN, secret snapshot и validation, restricted schema format, recursive materialization exact input references, exact name-to-URL lookup, run admission и slots, Kubernetes gateways, browser consumer, live Version rotation, pause-agent, destructive pre-production cutover и обновление `brand-size-chart` как реального acceptance source.

В scope не входят WireGuard и другие protocol adapters, Kubernetes billing и cost allocation, production EKS, transparent interception всего egress, SOCKS login/password, пользовательские static AWS keys, отдельный публичный VPN runtime REST API и migration старого test-only Product state. Gluetun является pinned internal component, а не user-facing API или новый Product owner.

## Имена, Владение И Публикация

Общий contract user-supplied machine `name`, optional mutable `label=''` и owner-prefixed public names принадлежит `design/domain.md`, разделу `Имена И Метки`. Для `VpnConfig` stable proxy name равен `{zitadel_user_id}/{vpn_config.name}`. Management list/detail возвращают только roots effective user; selector объединяет пригодные собственные roots и опубликованные пригодные чужие roots.

`Publish` принадлежит root `VpnConfig`, требует server admin capability, mutable lifecycle и ready active Version и не имеет `Unpublish`. Публикация доверяет owner менять active Version в дальнейшем без повторного admin action. Consumer не получает management detail, Version list, validation reports, secret metadata или bytes.

Archive доступен owner и запрещает новое использование, не прерывая существующие gateways. Archived, blocked и deleted root неизменяемы, кроме canonical lifecycle transition. Block/delete root и удаление active Version немедленно revoke-ят gateways и завершают affected runs platform error без fallback. Delete root и delete Version доступны только administrator capability и не ждут внешнего cleanup перед logical transition.

## `VpnConfig` И `VpnConfigVersion`

Root хранит immutable `name` и `protocol`, mutable `label` и positive `connection_slot_count` и nullable `active_version_id`. Поддерживается только `protocol="openvpn"`. `null` active pointer является реальным отсутствием Version. Снижение slot limit ниже current usage сохраняется без остановки existing gateways и блокирует новые allocations до освобождения capacity.

Version имеет immutable SemVer, exact Data snapshot и статусы `created`, `validating`, `waiting_for_connection_slot`, `testing`, `ready`, `failed`. Create немедленно запускает validation. Infrastructure failure автоматически продолжает ту же Version; deterministic config/test failure даёт `failed`. `ready` является immutable point-in-time proof exact snapshot и не меняется при будущей provider outage.

Set active принимает только ready Version mutable root. Последняя desired generation побеждает, повтор exact Version идемпотентен, новые runs используют latest pointer сразу, existing runs convergence-ятся асинхронно. Delete active Version одной transaction очищает pointer. Automatic fallback на другую Version отсутствует.

## Secret Snapshot И Validation

Storage contract принадлежит `design/data-storage.md`, разделу `Secret Data`. Canonical root Version равен `/.secret/vpn_config/{vpn_config.name}/`. `protocol` хранится на Product root, а protocol-neutral `config.json` использует `config_path` и optional credential pair. Snapshot фиксирует exact `DataRevision` и `DataObject` VersionIds; current path mutation не меняет Version.

Platform-owned validation выполняется exact final `vpn-runtime` image digest и проверяет static safety, стандартный DNS для provider hostname, реальное подключение, SOCKS5 TCP, proxy-side DNS только через tunnel, controlled HTTPS, fail-closed и clean shutdown. Этот digest сохраняется на Version и используется каждым её gateway; mutable current image не подменяет проверенную реализацию. Observed exit IP и redacted diagnostic сохраняются. Publisher не предоставляет tests или fixtures для `VpnConfigVersion`.

Development acceptance создаёт для восстановленного direct owner `VpnConfig name="tr"`, загружает существующие operator files `config.json` и `tr.ovpn` в `/.secret/vpn_config/tr/` через обычный AWS Secret upload/accept flow и создаёт Version из принятой revision. Старый logical `/.secret/openvpn/` и source-owned VPN secret binding удаляются.

## Schema И Input-Driven Routing

Restricted profile `input.schema.json` принимает `format: network-proxy-name` только на string value либо string `items` ordered unique array. В nullable field format остаётся только на точной non-null string branch либо на string `items` точной non-null array branch разрешённого `anyOf`; container, null branch и array node format не получают. Required, default и nullable semantics остаются у schema; отсутствующий optional field и разрешённый `null` не добавляют proxy name и дают direct egress consumer этого field. UI использует standard Select/MultiSelect, а backend при Workflow save и Run create проверяет exact stable values через `VpnConfig` selector policy.

Platform recursively обходит resolved exact schema вместе с полным input, сохраняет original field values/order, строит ordered deduplicated union всех names и materializes по одному gateway на root. Для этого union она строит единственную immutable map exact stable name в run-local SOCKS URL. Отдельные VPN bindings, aliases, defaults и inference ordinary strings запрещены.

Платформа не выбирает proxy для consumer и не интерпретирует ordered list как pool. String является одной explicit reference, а каждый array element — отдельной explicit reference; absent field, `null` и empty list не добавляют gateway. Каждый consumer читает exact stable name из своей конкретной source-owned настройки полного immutable `input.json` и выполняет exact lookup в map. Если source объявляет array, его domain contract явно связывает операции с конкретными elements либо явно обрабатывает весь list; platform round-robin, modulo, random, last-used state и fallback отсутствуют.

`workflow-container-runtime` предоставляет exact name-to-URL lookup, который возвращает только соответствующий map entry и отклоняет unknown name. Для browser-backed Codex step отдельная nullable настройка `mcp_playwright_network_proxy_name` живёт в `WorkflowStepCodexConfigBase`, помечается `network-proxy-name` и передаётся browser router без выбора; `null` означает direct browser egress. Action, verifier, retry и recovery используют одно immutable step-config value. Независимый image читает свои exact input fields и ту же capability map напрямую.

## Run Admission И Gateway

Create `WorkflowRun` snapshot-ит полный input и exact proxy root set, но active Version остаётся live pointer. Один root одного run создаёт один gateway Pod, stable Service, provider connection и slot независимо от числа SOCKS clients. Different roots/runs используют отдельные Pods и slots. SOCKS authentication отсутствует; namespace identity и `NetworkPolicy` изолируют endpoint. Клиентская `NetworkPolicy` использует стабильный run/config/proxy selector без Version и generation, тогда как stable Service выбирает exact Version/generation; это сохраняет неизменяемую policy и разрешает только обе generation одного управляемого live switch.

Multi-root run создаёт durable request всего ordered set и резервирует all-or-none одной transaction. Waiting order равен `(t_create, id)`. На каждом root request eligible, только если число более ранних waiting requests меньше free-slot count; старт разрешён только при одновременной eligibility всего set. Более поздний request не занимает slot, необходимый более раннему blocked request. Validation Version использует ту же queue как single-root request. Run остаётся `created` до reservation и readiness всех gateways. Provider outage автоматически retry-ится с concrete diagnostic без finite attempt count.

`vpn-runtime` использует pinned Gluetun engine, SOCKS5 implementation и отдельный DNS-forwarder целевых запросов. OpenVPN `remote` принимает hostname или буквальный IP-адрес; source snapshot не меняется, а каждая private provider attempt выполняет новый lookup через стандартный DNS Pod и передаёт pinned Gluetun только attempt-local IP. DNS управляющих процессов использует обычный интерфейс независимо от tunnel, а DNS UID процесса SOCKS перенаправляется в forwarder, чей upstream принудительно привязан к `tun0`. Ordinary reconnect сохраняет Pod, Service и slot, остаётся fail-closed и не вызывает workflow freeze. Завершение provider process или 30 секунд без readiness создают следующую attempt с новым lookup. Existing TCP может оборваться один раз, а client восстанавливается по собственному contract.

Image-visible capability содержит только referenced map:

```json
{
  "network_proxy": {
    "proxy_by_name_map": {
      "{zitadel_user_id}/{vpn_config.name}": "socks5://<run-local-service>:1080"
    }
  }
}
```

Workflow image и run-local `browser-runtime` получают один и тот же immutable map snapshot для полного extracted union. Другой map, consumer-specific materialization и повторное разрешение names отсутствуют.

## Browser Runtime

`browser-runtime` удаляет VPN code и принимает immutable proxy map как обычный consumer. Structural route value `network_proxy_name` приходит только из exact `mcp_playwright_network_proxy_name` step setting и разрешается exact lookup в соответствующий endpoint. Backend identity равен паре physical profile/proxy, и каждая пара получает отдельную working copy profile, поэтому тот же logical profile может одновременно работать через разные VPN без совместного `userDataDir`. Proxy difference не создаёт profile conflict, forced `browser_close` или writeback split. Candidate exact pair атомарно заменяет общий candidate; последняя успешная publication выигрывает. `null` setting запускает direct backend.

`browser_runtime` остаётся source-declared capability только для Playwright MCP/profile/writeback. `network_proxy` является standard environment capability, derived из input, и не имеет `is_vpn_enabled` или source-owned VPN secret.

## Live Active-Version Rotation

Один run switch owner coalesce-ит pending roots и latest desired generations, freeze-ит workflow/browser Pods один раз, заменяет outdated gateways параллельно и выполняет один continue. Способ создания каждой generation является immutable durable template input: initial gateway автоматически активируется при старте, prepared replacement ждёт отдельной activation, а завершение switch не переписывает этот признак ни у изменённой, ни у неизменённой historical generation. Different runs переключаются параллельно с platform limit Kubernetes API calls.

Replacement gateway Pod сначала находится в prepared state без provider connection. Pause-agent sidecar работает при `shareProcessNamespace`, поставляется отдельным от backend image только со своим runtime и обязательными зависимостями и управляется controller через exact-Pod-UID Kubernetes `pods/exec` commands `freeze`, `status`, `continue` и `terminate` с fenced generation. Этот доверенный platform container работает как UID `0` только с capability `KILL`, сбрасывает остальные capabilities, запрещает privilege escalation, использует read-only root filesystem и не имеет shell, service-account token или public network API.

Freeze посылает `SIGSTOP` всем target processes Pod, кроме sandbox PID 1 и pause-agent, и сканирует `/proc` до proof. Controller до первого вызова `freeze` сохраняет единый timestamp начала barrier, а agent сохраняет generation и timestamp до первой попытки сигналов; ошибка сигнала не уничтожает это состояние. Ранний proof принимает exited/stopped targets. Через 60 секунд с сохранённого начала первой команды `freeze` barrier считается доказанно пройденным даже при недоступном или неполном proof, и та же ветка продолжает замену. Fallback branch, early `SIGCONT` и reset timer отсутствуют.

После stop proof старого gateway prepared replacement подключается, достигает readiness и получает stable Service. То же переключение удаляет generation-local `NetworkPolicy` старого gateway по точному имени, не пересоздавая stable client policies. Затем все frozen Pods получают `SIGCONT`. Liveness не зависит от frozen workload, readiness false до continue, durable freeze исключается из heartbeat/deadline/lease.

Общий switch limit равен 300 секундам с того же сохранённого начала первой команды `freeze`. При превышении controller создаёт suspended replacement workflow execution, выполняет `terminate` с `SIGKILL` без `SIGCONT` и ждёт standard exact stop proof. После proof replacement восстанавливает последний accepted savepoint и latest active Versions. D-state не разрешает параллельного writer.

Если exact workflow Job останавливается в любой незавершённой switch state, controller не повторяет `freeze` и не выполняет `continue`. Recoverable outcome немедленно создаёт suspended replacement execution; surviving exact pause targets получают `terminate`, а уже отсутствующий exact Pod считается доказанно остановленным. Pause-agent возвращает `running` readiness без `SIGCONT` только после исчезновения сохранённых process identities. Replacement activation ждёт exact absence старых gateway Pods и standard predecessor stop proof, затем использует latest active Versions и последний accepted savepoint.

## `brand-size-chart`

Target Version `0.7.1` объявляет `browser_runtime`, удаляет `browser_vpn_runtime` и получает inherited explicit nullable `mcp_playwright_network_proxy_name` в каждом `WorkflowStepCodexConfigBase`. Каждый browser-backed step читает exact VPN root только из собственного standard single `network-proxy-name` field; `null` даёт direct egress. Global `network_proxy_name_list`, routing index и hidden distribution отсутствуют. Каждый заявленный `result/{brand_key}` и `workspace/{brand_key}` manifest-root существует до соответствующего safepoint даже при domain failure без chart-файлов. Pre-production Workflows пересоздаются; `0.6.x -> 0.7.x` compatibility field или input migration отсутствует.

`source_discover`, `coverage_decide` и `canonical_select` используют exact `mcp_playwright_network_proxy_name` своего сохранённого step config. Все concurrent invocations одного step используют одно и то же явно сохранённое name; если workflow должен маршрутизировать разные invocations через разные VPN, он обязан объявить соответствующие отдельные input settings и domain mapping, а не полагаться на platform selection. Step `InputT` не хранит proxy name, index или копию config, потому что exact config уже передаётся DBOS wrapper отдельно.

## Failure И Recovery

Run creation с invalid, inaccessible, archived, blocked, deleted или inactive proxy reference отклоняется concrete diagnostic. Root, ставший archived после run start, сохраняет existing gateway; block/delete или active Version delete останавливает gateway и fail-ит run. Active Version rotation сохраняет root slot и stable name.

Controller operations, slot reservations, gateway generations, validation phases, pause generations и cleanup intents durable и идемпотентны. Crash продолжает exact desired generation и не создаёт duplicate gateway, slot или workflow execution. Successful rotation удаляет старую generation policy после переключения stable Service; final cleanup удаляет и проверяет отсутствие всех run-owned gateway Pods и generation policies. Cleanup root logical transition не ждёт provider resources, но physical secret references освобождаются только после завершения materializations.

## Cutover И Совместимость

Cutover является разрушающим и выполняется до production. Product DB schema, local Kubernetes state и test rows пересоздаются; DB migration, data conversion, dual read/write, aliases и compatibility packages отсутствуют. Existing operator VPN files переносятся только обычным accepted Secret Data flow в новый canonical path.

Git repository и package `browser-vpn-runtime`, capability `browser_vpn_runtime`, `is_vpn_enabled`, `openvpn_config_name`, old image/command names и compatibility imports удаляются. GitHub redirect не является поддерживаемым contract. Новый `browser-runtime` содержит только browser implementation, новый `vpn-runtime` — только VPN implementation.

## Изменения По Владельцам

- `workflow-control-center` реализует Product models, standard API/UI resources, selectors, schema extraction, validation orchestration, slot queue, gateway Kubernetes topology, pause-agent control, active rotation, run provenance и destructive schema cutover.
- `workflow-container-contract` расширяет restricted schema profile и public validation для `network-proxy-name` без platform runtime dependency.
- `workflow-container-runtime` добавляет typed capability map, exact name lookup, `mcp_playwright_network_proxy_name` и browser route propagation, не запуская browser или VPN processes.
- `browser-runtime` удаляет VPN/OpenVPN/SOCKS server code, переименовывает package/images/commands и реализует independent profile/proxy backends.
- `vpn-runtime` реализует strict OpenVPN adapter, pinned Gluetun/SOCKS gateway, prepared activation, validation runner, fail-closed и clean shutdown.
- Плагин `workflow-container-agent-tools` обновляет authoring owner map, schema rules, explicit input-driven routing и audit boundary.
- `brand-size-chart` выпускает Version `0.7.1`, обновляет capability/input/step configs, использует exact configured proxy, создаёт каждый заявленный manifest-root до safepoint и остаётся first real UI acceptance workflow.

## Verification

### Product И Контракты

Behavior tests `workflow-control-center` покрывают owner-only management, own-plus-published selector, admin Publish, generic names/labels, Version states/active/delete/lifecycle, foreign detail redaction, exact secret snapshots, recursive schema extraction, single/multiple/empty proxy fields, inaccessible and stale references, deduplication, run snapshots и live active pointer.

`workflow-container-contract` проверяет valid string/array formats, required `uniqueItems`, local refs/nullable wrappers и rejection invalid placements. `workflow-container-runtime` проверяет exact name-to-URL lookup, unknown-name rejection, отсутствие selection state, immutable `mcp_playwright_network_proxy_name` propagation и browser route/lease identity. Каждый executable project выполняет commands своего `AGENTS.md`.

### VPN И Browser

`vpn-runtime` unit и real-container integration доказывают strict parser, unsafe path/directive rejection, hostname preservation, attempt-local IP rendering, repeated standard-DNS resolution, generated credential permissions, tunnel establishment, SOCKS5 TCP, разделение system DNS и proxy-side DNS, controlled HTTPS, observed exit IP, fail-closed, provider-attempt replacement, prepared activation without second provider connection и clean shutdown. Platform integration дополнительно запускает ordinary non-browser workflow HTTP client, читает stable name из конкретного input field, выполняет exact lookup в `proxy_by_name_map` и доказывает тот же controlled egress без automatic selection. `browser-runtime` доказывает direct/proxy modes, unknown-name rejection, same-profile/different-proxy concurrency, independent outputs и unchanged writeback semantics.

### Scheduling И Recovery

Local Kubernetes integration покрывает all-or-none multi-root FIFO reservation, slot lowering, validation/run competition, stable Services, initial readiness, ordinary reconnect without freeze, latest-wins parallel rotation, exact Pod UID and generation fencing, 60-second single branch, 300-second suspended replacement, отсутствие early `SIGCONT`, D-state waiting, exact stop proof, no overlapping writer и restore latest accepted savepoint.

### UI И End-To-End

UI verification следует обязательному порядку `AGENTS.md`: tests/build, `python tool/local_kubernetes_manage.py apply` без force rebuild, `pytest test/ui -q` и manual current-assets browser flow на `http://localhost`. Проверка создаёт owner `VpnConfig` и Versions, наблюдает validation, назначает active, выполняет admin Publish, подтверждает owner-only management и foreign selector, редактирует standard single/multiple fields и видит concrete failures. Visual changes сравниваются с analogous shared components и фиксируются screenshots.

Real acceptance загружает `tr` config в AWS development Secret bucket, использует не менее двух ready VPN roots с distinguishable exit IPs, создаёт `brand-size-chart 0.7.1` Workflow через UI, задаёт разные exact names в двух browser step settings и запускает run с `source_discover.concurrency` не меньше двух. Проверка доказывает, что map содержит union обоих names, каждый step использует только собственную настройку, concurrent invocations `source_discover` не распределяются по другим names, browser profile/proxy pairs остаются изолированными, Data/result/dataset transitions успешны, live active-Version switch сохраняет stable name/Service, удаляет obsolete generation policy, workflow executions не пересекаются и run завершается после rotation/recovery. Production AWS resources и Kubernetes cluster не используются.

### Семантическая Приёмка

Перед handoff перечитываются все изменённые owner contracts и фактические public models/configs как один набор. В target state отсутствуют duplicate VPN owner, automatic proxy selection/distribution, routing index/state, stale combined runtime name, compatibility bridge, raw secret exposure, отдельный Workflow VPN binding и противоречие lifecycle/failure/recovery.
