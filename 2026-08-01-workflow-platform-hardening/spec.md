# Укрепление Release, Development-Environment И VPN Runtime

## Назначение

Эта dedicated implementation specification координирует один cross-repository change-set в `agent-plugins`, `project-goals`, `project-standards`, `workflow-infrastructure`, `workflow-control-center` и `vpn-runtime`. Он закрывает найденные после архитектурного аудита разрывы crash-safe registry retention, разделяет два перегруженных orchestration owner, переносит EC2 bootstrap из CloudFormation shell в проверяемый Python, создаёт единственного account-foundation owner, добавляет обычный Session Manager shell audit, заменяет публичную VPN validation endpoint на одноразовый private S3 nonce, удаляет WorkflowRun freeze из VPN rotation, вводит stable fail-closed proxy, связывает каждый task worktree с отдельной development environment и переводит goal artifacts, checkpoints, merge и cleanup на единый tracked coordination protocol.

Устойчивые reusable task-worktree semantics принадлежат `agent-plugins/plugins/agent-workflows/skills/goal-brainstorm/references/specification-contract.md` и `agent-plugins/plugins/agent-workflows/skills/goal-brainstorm/references/worktree-contract.md`. Общая infrastructure architecture принадлежит `workflow-infrastructure/design/environment-model.md`, development specialization — `workflow-infrastructure/design/development-environment.md`, future production design — `workflow-infrastructure/design/production-environment.md`. Product runtime и VPN domain semantics принадлежат `workflow-control-center/design/workflow-runtime.md`, `workflow-control-center/design/domain.md` и `workflow-control-center/design/backend.md`. Provider-local execution contract принадлежит `vpn-runtime/DESIGN.md`.

Эта specification владеет только общей task boundary, согласованным pre-production cutover и acceptance нескольких stable owners. После implementation она не остаётся единственным owner ни одного durable rule.

## Проверенное Исходное Состояние

Development использует выделенный AWS account `463564115167`, region `us-east-1` и local profile `workflow-control-center-devel`. Пользователь предоставил standing authority на необходимые изменения внутри этого development account без отдельной approval pause. Future production account не назначен, production resources отсутствуют и не входят в эту задачу.

Текущая implementation имеет следующие подтверждённые проблемы:

- Product release использует opaque `buildx --push`, а retention reachability читает только законченный `release-manifest.json`; crash после любого registry manifest PUT, но до durable полного graph/final manifest может оставить root index, runnable либо SBOM/provenance attestation manifests, которые ordinary Distribution GC без `--delete-untagged` не удалит;
- `HostArtifactResolver` одновременно владеет download, Git refs, HTTP cache, cryptographic verification и provider-specific installation, а `ProductReleaseManager` смешивает source graph, chart, image, manifest, rollback, recovery и retention;
- EC2 CloudFormation UserData содержит крупный multi-owner Bash bootstrap;
- обычная Session Manager shell не имеет account-level encrypted command/output logging, хотя SSH и port-forward metadata уже наблюдаемы через AWS session/CloudTrail state;
- VPN validation использует публичную anonymous API Gateway method;
- WorkflowRun active-Version rotation связывает VPN с WorkflowRun freeze/replacement через pause-agent, `shareProcessNamespace`, `SIGSTOP`/`SIGCONT` и VPN-specific execution state;
- VPN runtime использует один `activation_timeout_seconds` последовательно для нескольких phases, 30-second reconnect timeout короче OpenVPN/Gluetun normal behavior, 10-second process stop ниже Kubernetes standard grace, а `reconnect_poll_seconds` смешивает health polling и retry delay;
- development environment identity и AWS resource names содержат project name, task worktree не имеет обязательной отдельной full environment, а завершённая `.spec` pair удаляется независимо от AWS resources, worktrees и task branches;
- отдельного `goal-delete` workflow нет: текущая preparation state не bind-ит project-owned external cleanup к одной resumable resources/worktrees/refs/artifacts transaction, а pair сохраняется независимо от worktree cleanup;
- task artifacts физически живут в project-local ignored `.spec`, checkpoint identity отсутствует, cross-repository merge не имеет одного atomic published commit set, а technical `goal_ready` ошибочно закрывает revision до пользовательского approval;
- bootstrap manifests используют TOML, а WCC development overlay сохраняет extension `.yml`, хотя ни один owning ecosystem не требует эти форматы.

До первой подготовки исходные четыре Product/infrastructure main worktree были чисты и совпадали со своими upstream `main`; physical pair, task branches и task worktrees для common prefix отсутствовали. После пользовательских уточнений sealed, но не активированный candidate штатно возвращался командой `revise` в `repository_prepared`, а participant set был расширен до шести task roots ниже. Persistent goal для этой пары никогда не создавался и не активировался. Remote `project-goals` был пуст; для технической возможности создать его единственный bootstrap task worktree по старому контракту локально создан непубликованный empty root commit `1a7ab60`, не содержащий project files или task artifacts.

## Требуемый Результат

После implementation:

1. Каждый Product image сначала существует как validated retained OCI archive. До первого registry PUT durable release-local candidate ledger уже содержит archive identity и полный OCI manifest graph, достаточный для exact publication, recovery и root/runnable/attestation manifest deletion при crash до final release manifest или во время sweep.
2. Host artifact resolution и Product release lifecycle разделены на cohesive packages; managers владеют только sequence и explicit dependency wiring.
3. CloudFormation UserData только обеспечивает Session Manager. После регистрации managed node operator запускает exact versioned SSM Command document: `aws:downloadContent` получает content-addressed Python/bootstrap artifacts, минимальный launcher проверяет их SHA-256 и вызывает `host_bootstrap.py`; вся host configuration живёт в проверяемых Python owners.
4. Account-global development guards и Session Manager preferences имеют одного account-foundation owner; environment stacks только используют либо проверяют этот state.
5. VPN validation не публикует anonymous endpoint и не выдаёт AWS credentials validation Pod.
6. Один stable fail-closed proxy отделяет immutable proxy URL от сменяемых VPN gateway generations. WorkflowRun freeze/pause-agent contour полностью удалён.
7. Initial WorkflowRun остаётся `created`, пока все явно присоединённые VPN одновременно не готовы. После начала workflow workload состояние VPN не меняет WorkflowRun state, deadlines либо execution lifecycle.
8. VPN connection, recovery и stop policies имеют однозначные semantics и defaults, основанные на upstream contracts и real ARM64/k3s measurements; health polling и retry backoff больше не смешаны.
9. Один task common prefix получает одну полную isolated development environment, общую для всех participating repository worktrees этой задачи.
10. Task AWS resources используют exact tag `git-worktree=<common-prefix>` и account-local names без project name.
11. Все следующие task artifacts живут в tracked `project-goals/<common-prefix>/{spec.md,goal.md,checkpoint.yaml}` и commit-ятся и push-ятся прямо в canonical `project-goals/main`; этот coordination repository не имеет task branches, linked worktrees либо bootstrap manifest. Новые project-local `.spec`, task-artifact links и compatibility readers отсутствуют после one-time bootstrap cutover. Уже существующие ignored pairs остаются inert и не удаляются без отдельного explicit request. Эта task остаётся единственным bootstrap-исключением: она до конца выполняется по старому контракту через current physical pair и отдельный `project-goals` task worktree, а physical pair сохраняется до explicit `goal-delete`.
12. `goal_ready` означает только sealed review candidate. До activation ordinary `revise` сохраняет task identity, разрешает изменения и расширение participant set и требует повторного authoring/semantic review/seal.
13. `goal-checkpoint` атомарно публикует полный cross-repository commit snapshot, отдельный exclusive `goal-merge` возобновляемо merge-ит ровно один checkpoint и принимает его на primary environment, а `goal-delete` отдельно выполняет explicit resources → worktrees → refs → task-directory cleanup.
14. Project-owned machine-readable format следует YAML-by-default contract с extension `.yaml`; JSON/TOML остаются только у idiomatic external owners.
15. В development account не остаются compatibility names, legacy VPN freeze state, старый public validation endpoint, прежние flat release owners либо прежний Bash bootstrap. Универсальные database migration, versioning, rollback и recovery mechanisms текущей платформы сохраняются.

## Scope И Non-Goals

В scope входят:

- stable owner documents и implementation во всех шести participating repositories;
- retained Product OCI staging, candidate image ledger, durable complete OCI manifest graph, trusted root-last publication и exact registry manifest cleanup;
- hierarchical package split двух central orchestration owners;
- Python host bootstrap bundle, SSM-agent-only UserData и exact versioned bootstrap Command document;
- account-foundation stack, primary-only AWS Backup ownership и Session Manager preferences;
- S3 nonce VPN validation;
- stable proxy runtime/control protocol и WCC Kubernetes orchestration;
- slot-preserving active-Version rotation с spare-slot и no-spare-slot paths;
- removal pause-agent/freeze/replacement coupling;
- initial all-VPN WorkflowRun admission barrier;
- immutable per-`VpnConfigVersion` timing policy и calibrated defaults;
- worktree-derived AWS environment identity, tags, names, source selection, tunnel endpoint и cleanup;
- central `project-goals` task artifacts, checkpoint schema и lifecycle;
- separate `goal-checkpoint`, exclusive `goal-merge` and `goal-delete` workflows;
- reusable YAML bootstrap cleanup hook, durable cleanup journal и Git resource retirement;
- YAML-by-default shared standard и согласованный WCC-related format cutover;
- pre-production reset/recreate необходимых development resources и Product state;
- deterministic, integration и real development-account acceptance;
- logical commits и push всех participating task branches.

В scope не входят:

- создание или изменение production AWS account, Amazon EKS либо production resources;
- validation deployment в production;
- параллельная production implementation;
- несколько EC2 nodes внутри одной development environment;
- billing/chargeback;
- автоматический выбор proxy платформой;
- сохранение старых AWS names, public VPN validation endpoint, freeze/pause-agent state либо compatibility readers;
- удаление generic database migration framework либо generic version/recovery mechanisms;
- автоматическое удаление task artifacts только потому, что goal стал complete;
- использование `project-goals` как Product source repository либо включение его в self-referential checkpoint project list;
- создание coordination task branch, linked worktree либо bootstrap manifest в `project-goals` после завершения этой bootstrap task;
- raw filesystem watcher, который пытается реагировать на ручной `rm` task artifact;
- отдельные persistent evidence/ledger documents для completion audit.

## Владение Репозиториями

### `agent-plugins`

`agent-workflows:goal-brainstorm` владеет reusable task contract preparation, pre-activation revision, worktree isolation и activation boundary. `agent-workflows:goal-checkpoint`, `agent-workflows:goal-merge` и `agent-workflows:goal-delete` раздельно владеют checkpoint publication, one-checkpoint merge/acceptance и explicit synchronized deletion. Project-specific AWS names, commands и resource logic в plugin запрещены.

Shared lifecycle primitives получают один нижний reusable owner; skill-specific orchestration остаётся в cohesive owners и не увеличивает responsibilities существующего крупного `worktree.py`. Skill descriptions, references, behavior corpus, CLIs и real-Git tests изменяются согласованно. Project-local `.spec` contract и task-artifact symlink implementation удаляются после central cutover без compatibility reader.

### `project-goals`

Repository владеет tracked `<common-prefix>/spec.md`, `goal.md` and `checkpoint.yaml`, closed checkpoint schema, accepted checkpoint pointer и stable task-artifact lifecycle. Он не содержит Product runtime либо implementation source, не входит в self-referential checkpoint `project_list` и после bootstrap использует только canonical checkout ветки `main` без task branches, linked worktrees и `worktree-bootstrap.*`.

Текущая task является one-time bootstrap и продолжает использовать старую physical `.spec` pair, общий task branch и prepared `project-goals` worktree из `Task Identity`. После завершения owner authoring и executable protocol verification exact bootstrap publication создаёт remote `main` только с repository source contracts и exact central task directory; provider-created `.gitignore` и `worktree-bootstrap.toml` текущего bootstrap worktree в `project-goals/main` не публикуются и удаляются вместе с этим worktree при explicit `goal-delete`. Exact published commit фиксируется в private task state и доказывается перед первым checkpoint. Repository не включает собственный commit в self-referential `project_list`; остальные пять implementation repositories образуют первый full checkpoint.

После cutover каждый future lifecycle owner изменяет exact task-directory delta короткой workspace-global serialized transaction непосредственно в canonical `project-goals/main`: проверяет clean synchronized checkout, создаёт ordinary fast-forward commit, выполняет compare-and-swap push без force и возвращает checkout в clean synchronized state. Concurrent change другого task directory переигрывается на новый remote tip, а same-task change является semantic conflict. `goal-brainstorm` больше не создаёт для coordination repository branch, worktree либо manifest; task branches и worktrees остаются только у implementation repositories.

### `project-standards`

`project-standards:project-foundation`, reference `Machine-Readable Format Contract`, владеет YAML-by-default selection, `.yaml` extension и strict YAML 1.2 boundary. JSON/TOML exceptions определяются только owning external ecosystem, protocol либо tool. Consumer projects не копируют этот standard.

### `workflow-infrastructure`

Репозиторий владеет:

- account-local environment identity и AWS names;
- `git-worktree` tag propagation;
- common-prefix to environment-name derivation и collision proof;
- full task environment provisioning, source resolution, access, deployment and destruction;
- account-foundation stack;
- host artifact resolver package, bootstrap bundle и host bootstrap owners;
- immutable infrastructure source delivery, host release pointers и вызов WCC-owned Product release lifecycle без второго candidate-ledger owner;
- project-owned idempotent external cleanup hook;
- real AWS cleanup and task-environment acceptance.

### `workflow-control-center`

Репозиторий владеет:

- shared `lib/oci_registry` content-addressed primitives used by both Product and WorkflowSource publication without domain ownership;
- Product release image ledger, existing WorkflowSource build publication/cleanup graph ledger, manifest deletion and registry GC behavior;
- Product-side hierarchical release/image/retention packages;
- `VpnConfigVersion` persisted/API timing policy;
- VPN stable proxy and gateway Kubernetes resources, slots, readiness, rotation and cleanup;
- removal WorkflowRun pause/freeze state and resources;
- initial WorkflowRun all-VPN admission barrier;
- S3 nonce creation/cleanup and validation Job delivery;
- Product API/UI diagnostics and applicable Product tests.

### `vpn-runtime`

Репозиторий владеет:

- provider process lifecycle and exact runtime status;
- stable proxy implementation and private generation-fenced control socket;
- fail-closed upstream switching;
- provider attempt replacement, system DNS re-resolution and retry behavior;
- connection/recovery/stop timing semantics;
- process-group cleanup and real `/dev/net/tun` integration verification.

## Registry Candidate Ledger И Retention

Перед Product build release manager создаёт внутри exact candidate release directory durable candidate image ledger. Ledger write использует atomic replacement, file fsync и parent-directory fsync. Initial intent содержит repository/tag/platform/owner/candidate identity, но не разрешает registry mutation.

Каждая entry содержит как минимум:

- unique exact repository;
- unique exact temporary tag;
- target platform;
- intended image owner;
- immutable release-local OCI archive path, byte length и SHA-256 после local build;
- exact root digest;
- complete manifest graph с каждым repository-local manifest node и descriptor edge, достижимым из root index;
- complete blob descriptor inventory для publication verification и ordinary GC boundary;
- publication phase и exact completed registry objects;
- monotonic candidate identity достаточную для rejection чужого tag reuse.

Buildx использует `--pull`, `--provenance=mode=max`, `--sbom=true` и OCI exporter во временный файл на том же retained filesystem. Прямой registry exporter и `buildx --push` запрещены. Crash во время local build удаляет только incomplete local output: registry ещё не изменялся. После успешного build manager рекурсивно читает raw OCI bytes, проверяет expected image/platform identity и complete graph, fsync-ит archive, атомарно переносит его в immutable staging path и fsync-ит полный ledger до первого registry blob/manifest PUT.

Complete graph включает root index, runnable platform manifests и все in-index SBOM/provenance attestation manifests; config/layer descriptors входят в byte inventory, но остаются blobs для ordinary GC. Текущий Product принимает только attestations, явно достижимые из image index; скрытая referrer-only layout либо unknown manifest media shape fail closed отклоняется.

Trusted publisher не запускает Buildx и не получает mutable build context. Он идемпотентно публикует только recorded OCI bytes: blobs first, затем child manifests и последним root index под exact temporary tag. После ambiguous registry response publisher проверяет наличие exact digest прежде чем продолжить. Publication может начаться только из ledger с complete graph и matching archive SHA-256. Final release manifest может быть создан только после proof каждого recorded registry object и exact tag-to-root binding. Retained staging archive удаляется только после durable accepted final manifest либо после complete candidate manifest cleanup. Crash recovery продолжает exact recorded bytes и никогда не пересобирает image.

Retention до inventory одной DB transaction закрывает durable registry-root admission fence с exact operation identity/generation. Все transitions, способные принять новый OCI root либо сменить current/rollback/root-bearing runtime identity, сериализуются через ту же singleton row и при закрытом fence сохраняют только retryable/pending intent. Crash не открывает fence по timeout; recovery продолжает exact generation. После writer quiescence и SQL idle manager durable фиксирует immutable complete inventory этой generation. Затем inventory читает candidate ledger независимо от наличия либо валидности final release manifest. Incomplete graph допустим только до registry mutation и позволяет удалить incomplete local staging. Publication phase с incomplete graph является invariant violation и fail closed запрещает deletion. Inventory отдельно строит union candidate graph и transitive rooted graph. Для каждой unrooted candidate root group:

1. использовать только записанные root digest и complete graph, а exact tag применять как verification/recovery binding, но не как источник потерянного graph;
2. проверить repository/tag ownership, root identity и complete graph media/descriptor shape;
3. сгруппировать все unrooted ledger entries с тем же exact `(repository, root digest)` и отдельно повторно проверить top-level external reachability по rooted releases, accepted source Versions/runs и ledger entries, принадлежащим rooted releases;
4. если exact root digest имеет external root, сохранить все unrooted group ledgers/directories: Distribution удаляет manifest только по digest, а не один temporary tag, поэтому ledger понадобится после исчезновения root;
5. иначе вычислить `candidate manifest closure - rooted manifest closure` для всего repository, чтобы runnable либо attestation child, общий с другим rooted root, никогда не удалялся, а child, общий только нескольким jointly unrooted roots, не становился ложным root;
6. durable зафиксировать complete deletion set, удалить каждый manifest digest из него ровно один раз, начиная с candidate roots/indexes и идемпотентно продолжая remaining runnable/attestation manifests после crash;
7. доказать отсутствие каждого удаляемого manifest и каждого exact recorded temporary tag группы;
8. удалить staging archive и release directories группы только после complete graph cleanup; shared rooted child может физически оставаться без сохранения уже завершённого unrooted ledger;
9. выполнить existing blob GC без `--delete-untagged` только в доказанном read-only idle safepoint.

Crash в любой external publication или deletion phase оставляет достаточный durable intent и exact source bytes для idempotent continuation, включая crash после child-manifest PUT до root PUT либо после удаления root index до удаления untagged attestation manifests. Неполный release directory без ledger, missing/mismatched staging bytes у незавершённой publication либо underdetermined ownership закрывает deletion. Registry-root admission открывается только после complete cleanup, read-write registry/writer restoration, отсутствия GC marker и exact generation proof. Ordinary Distribution GC не считается заменой manifest deletion.

Тот же graph invariant применяется к уже существующему `WorkflowSourceVersionBuildImageCleanup` без новой доменной сущности. Trusted worker валидирует local OCI archive и durable сохраняет exact immutable staging identity/path, byte length/SHA-256, repository, unique candidate tag, root digest, complete root/runnable/in-index-attestation graph и publication state до первого registry blob/manifest PUT. После crash только matching exact staged bytes могут продолжиться; missing/mismatched bytes фиксируют infrastructure failure и известный graph уходит в cleanup без недоказанной rebuild substitution. Staging удаляется только после durable complete-publication proof либо complete cleanup. Accepted graph остаётся retained provenance ready Version; failed/прерванный unreferenced graph становится due не раньше одного дня. Build cleanup и Product maintenance используют один complete rooted closure для ready Versions, active/recoverable runs и active publications, сериализуют manifest mutation общей registry-activity boundary, сохраняют shared rooted child и удаляют jointly unrooted child ровно один раз. Build cleanup удаляет due WorkflowSource manifests; Product maintenance остаётся единственным owner read-only blob GC для orphan blobs.

## Orchestration Package Boundaries

### Host Artifacts

Существующий host artifact owner превращается в package с отдельными owners для:

- immutable models and resolved identities;
- byte download/cache boundary;
- Git ref resolution/export;
- trust, checksum and signature verification;
- provider-specific AWS CLI, Docker, Python/uv, k3s and Helm resolution/install contracts;
- facade resolver, который только выбирает provider и связывает explicit dependencies.

Provider не владеет orchestration других providers. Download, verification и filesystem exposure не копируются по provider modules. Facade не содержит installation implementation branches beyond dispatch.

### Product Release

Общие content-addressed OCI primitives принадлежат одному `workflow-control-center/lib/oci_registry/`: immutable graph model, OCI-layout reader/validator, Distribution transport, root-last idempotent publisher и manifest-deletion planner. Они не знают ORM, Product roots или release lifecycle и одинаково используются WorkflowSource publisher и Product tooling; второй registry/graph implementation запрещён.

Product release становится hierarchical package с отдельными owners для:

- models;
- source graph;
- chart/render artifacts;
- `image/` с отдельными model, staging, ledger и sequencing owners вокруг shared OCI primitives;
- final manifest;
- recovery contract;
- rollback/current markers;
- `retention/` с отдельными model, Product reachability и sequencing owners;
- sequence manager.

Top-level и nested managers не владеют parsing, storage formats, registry protocol, reachability algorithm либо recovery validation. Flat `release.py`, flat sibling `image.py`/`retention.py`, compatibility modules, mixins и pass-through wrappers после migration удаляются. Imports и tests переходят на единственный current package surface.

## EC2 Bootstrap И Account Foundation

CloudFormation UserData больше не устанавливает packages, не парсит host manifest, не инициализирует EBS/k3s/systemd/network и не содержит embedded multi-owner Bash. Он только обеспечивает установленный/запущенный SSM agent через AMI-supported path и завершает cloud-init; UserData не притворяется caller-ом SSM document plugin.

Environment CloudFormation создаёт bootstrap `AWS::SSM::Document` как `DocumentType: Command` с `UpdateMethod: NewVersion`. Поскольку CloudFormation не выполняет drift detection этого resource, после регистрации managed node operator явно проверяет `Status=Active`, exact numeric `DefaultVersion=LatestVersion`, ожидаемый content identity и system-created SHA-256 environment-owned document через `DescribeDocument`/`GetDocument` и передаёт те же numeric `DocumentVersion`, `DocumentHash` и `DocumentHashType=Sha256` в `SendCommand`; `$LATEST`, `$DEFAULT` и invocation только по name запрещены. Его `aws:downloadContent` steps получают exact Python 3.14 runtime artifact и exact content-addressed bootstrap bundle из private content-addressed S3 objects. Следующий минимальный `aws:runShellScript` launcher проверяет recorded SHA-256, извлекает artifacts в private root и запускает root `host_bootstrap.py`; любой mismatch или command failure закрывает create/replacement acceptance. Document version/hash, artifact identities и digests являются immutable inputs compute change set, а instance profile разрешает read только exact environment artifact boundary.

Python bootstrap package разделяет owners artifacts, storage, Product-owned systemd services, k3s и network. Он не устанавливает и не перенастраивает SSM agent, поскольку этот prerequisite уже доказан AMI-supported UserData path до `SendCommand`. Все действия idempotent, тестируемы и имеют redacted diagnostics. Shell остаётся только минимальным launcher и не образует параллельный implementation contour.

Единственный account-foundation stack development account владеет:

- account-level S3 Block Public Access;
- Lake Formation account settings;
- encrypted CloudWatch Log Group с retention;
- account-level `SSM-SessionManagerRunShell` preferences;
- primary-only AWS Backup plan/selection and shared account guards.

Environment stacks не создают competing account-global owners. Они перед apply и после него fail closed проверяют exact account-foundation state.

Primary retained EBS использует AWS Backup crash-consistent recovery. Task environments не получают scheduled backup. One-time task volume copy snapshot удаляется после доказанного copy completion.

Обычная Session Manager shell пишет command/output в encrypted CloudWatch owner. SSH-over-SSM и port-forwarding имеют только session/CloudTrail metadata; content audit для них не заявляется и idle decision его не требует. Idle detection использует наличие sessions и Product activity, а не command content.

## VPN Validation Без Public Endpoint

После выдачи slot и непосредственно перед Job creation WCC validation controller создаёт private random nonce object в platform-only `vpn-validation/` prefix environment Observability bucket и presigned HTTPS GET URL. Tenant Data/Secret/Result roles не получают этот prefix. Signing session обязана жить дольше полного bounded scheduling/image-pull, validation, stop и transport budget; иначе controller сначала обновляет renewable credentials. URL покрывает тот же budget с margin и передаётся Job через отдельный mounted Kubernetes Secret file; URL и nonce не попадают в args, logs, status либо report.

Validation выполняет exact HTTPS GET через SOCKS и проверяет exact nonce bytes. Это доказывает tunnel egress, proxy-side DNS и fail-closed behavior без public API Gateway method и без AWS credentials в Pod.

После result controller удаляет nonce object и Kubernetes Secret. One-day S3 Lifecycle удаляет abandoned nonce objects как failsafe. Scheduling/expiry failure до result является transient attempt: bounded cleanup освобождает slot и следующая attempt заново входит в fair queue. Истечение URL после validation не влияет на gateway или WorkflowRun.

`observed_exit_ip` удаляется из runtime report, API, ORM, UI, tests and design. Public anonymous validation API Gateway, invoke Lambda, throttle and associated IAM resources удаляются.

## Stable Fail-Closed Proxy

Один referenced proxy name одного WorkflowRun получает stable proxy Pod/Service, адрес которого не содержит `VpnConfigVersion` либо generation. Gateway row при создании snapshot-ит exact stable-proxy image digest Product release этого run; restart не подхватывает новый platform release. Image собирается отдельным minimal `vpn-runtime-stable-proxy` OCI target вместо privileged gateway image. Stable proxy:

- не получает VPN config, credentials, `/dev/net/tun`, AWS credentials или Kubernetes API access;
- принимает traffic только от exact run consumers;
- может обращаться только к exact current gateway upstream по NetworkPolicy;
- fail closed не имеет default/direct upstream;
- exposes only private generation-fenced Unix control socket inside owner boundary;
- поддерживает operations disable, set exact upstream generation и status;
- переключает upstream атомарно после exact readiness proof.

Каждый proxy runtime start создаёт новый non-secret random instance identity и всегда начинается disabled, включая container restart внутри прежнего Pod. Mutation bind-ится к exact expected Pod UID, runtime instance identity и generation; daemon сверяет собственный UID из Downward API, а Controller проверяет UID/status до и после command. Applied proof сохраняет ту же tuple, поэтому Pod-name reuse и same-Pod container restart не восстанавливают cached upstream. Controller разрешает egress `NetworkPolicy` новой generation до switch, повторно доказывает readiness, применяет generation-fenced upstream и только после status proof удаляет egress старой generation. Consumer policy и Service selector остаются stable и никогда не выбирают gateway generation.

HAProxy может использоваться как implementation component, но raw runtime API наружу не публикуется. `vpn-runtime` владеет protocol wrapper и status, WCC — Kubernetes orchestration and durable desired/applied generation.

Workflow image и browser runtime получают только immutable map:

```json
{
  "network_proxy": {
    "proxy_by_name_map": {
      "{zitadel_user_id}/{vpn_config.name}": "socks5://<stable-run-local-service>:1080"
    }
  }
}
```

Платформа не выбирает proxy и не распределяет calls. Каждый consumer использует exact name из своего `input.json` field.

## WorkflowRun VPN Admission Boundary

Create WorkflowRun одной DB transaction snapshot-ит ordered deduplicated set всех schema-marked proxy names, immutable Product release/runtime graph в initial execution row и создаёт all-or-none slot request. Run остаётся `created`; subsequent deploy/retention не меняет и не теряет его runtime graph.

После reservation Controller параллельно создаёт stable proxies и initial gateway generations. Workflow workload Job, browser workload и их business processes не запускаются, пока одновременно не доказаны для каждого referenced proxy:

- exact desired gateway Pod/generation;
- Gluetun tunnel readiness;
- tunnel-bound DNS and SOCKS readiness;
- stable proxy exact upstream generation and fail-closed control status.

Readiness barrier не имеет finite provider-health retry count. Provider outage оставляет run в `created` с отдельными redacted VPN diagnostics; retries продолжаются, а cancel освобождает resources. Controller запускает workflow workload только после одного exact all-ready observation. После activation race с будущим VPN outage является обычной runtime network failure, а не причиной менять WorkflowRun state.

После перехода к working lifecycle:

- VPN state не меняет WorkflowRun state, execution state, deadline, heartbeat или replacement policy;
- VPN outage/rotation оставляет stable endpoint fail closed;
- browser и workflow software самостоятельно переживают или retry-ят connection errors;
- WorkflowRun freeze, pause-agent, `shareProcessNamespace`, `SIGSTOP`, `SIGCONT`, VPN switch state/diagnostic/timestamps и VPN-triggered execution replacement отсутствуют.

## Slots И Active-Version Rotation

Base `VpnConnectionSlotReservation` сохраняется за exact run/config до gateway cleanup.

При наличии свободного slot Controller получает transient surge reservation, полностью подключает новую generation и проверяет readiness. Одна transaction переназначает base reservation new generation, а surge reservation retiring old generation до proxy switch. После atomic switch old generation останавливается, а surge освобождается только после absence proof.

При отсутствии свободного slot Controller:

1. создаёт новую generation Pod в prepared state без provider connection;
2. выполняет static preparation, image startup и config materialization; optional DNS prefetch не является attempt identity;
3. сохраняет base reservation за этим run/config, не возвращая slot общей queue;
4. fail-close stable proxy и доказывает disconnect/stop старого provider;
5. одной transaction передаёт тот же reservation token prepared generation;
6. непосредственно перед provider process повторно выполняет authoritative system-DNS resolution и немедленно запускает connection;
7. после readiness атомарно переключает stable proxy и удаляет old generation.

Competitor не может занять retained reservation. Один existing TCP connection может оборваться; platform не замораживает consumer. Если новая generation не готова, proxy остаётся fail closed, gateway status остаётся reconnecting и provider attempts продолжаются. Старый WorkflowRun lifecycle не изменяется. Block/delete root либо удаление active Version независимо отзывает все affected proxy→gateway egress policies и отправляет generation-fenced disable, затем останавливает every affected current/prepared generation и освобождает все affected base/surge reservations только после их absence proof, не дожидаясь завершения run; stable proxy остаётся fail closed до обычного run cleanup. Пока root остаётся blocked/deleted, ожидающий initial run остаётся `created` до cancel. После допустимого canonical lifecycle recovery root либо назначения новой active Version у всё ещё mutable root каждый non-final referencing run создаёт новый ordinary base-slot request: initial run продолжает admission только после all-ready, а уже активированный run не меняет state и асинхронно восстанавливает тот же stable endpoint.

Active Version является latest desired generation. Несколько быстрых updates coalesce-ятся по latest desired version отдельно для каждого gateway. Archive запрещает новые reservations; общая immutable lifecycle semantics blocked/archived/deleted objects остаётся stable Product contract.

## VPN Timing Contract

Publisher source `.ovpn` не переписывается. Разрешённые native OpenVPN `connect-timeout`, `connect-retry`, `ping-restart` и related directives работают внутри outer gateway deadlines; outer runtime deadline всегда ограничивает exact process attempt.

Immutable `VpnConfigVersion` сохраняет concrete non-null positive integer fields:

- `connection_attempt_timeout_seconds`;
- `provider_recovery_grace_seconds`;
- `process_stop_timeout_seconds`.

Omitted create fields materialize exact defaults; explicit JSON `null` rejected. Значения обязаны попадать в documented platform safety ranges: upper bounds защищают slot/termination capacity, но не угадываются как прежние `600`/`60`. До завершения implementation ranges выводятся из real ARM64 failure/stop measurements и явно принятого maximum resource-ownership budget, затем одинаково фиксируются в stable design, API schema, ORM и runtime. Publisher value остаётся конкретным immutable значением каждой Version внутри этих bounds.

Baseline defaults:

- `connection_attempt_timeout_seconds = 180`;
- `provider_recovery_grace_seconds = 180`;
- `process_stop_timeout_seconds = 30`.

`connection_attempt_timeout_seconds` является одним monotonic deadline от provider process start до Gluetun, tunnel-bound DNS, SOCKS и gateway readiness. Он не reset-ится между phases и не включает Kubernetes scheduling/image pull.

`provider_recovery_grace_seconds` начинается после exact unhealthy observation и позволяет текущему Gluetun выполнить internal auto-heal. После expiry runtime fail-close user plane, останавливает только owned provider attempt, повторно разрешает source hostname standard system DNS, материализует новую private attempt и запускает её в том же gateway Pod. Gateway Pod заменяется только при failure supervisor/control protocol, Pod loss либо недоказанной process cleanup.

`process_stop_timeout_seconds` является одним общим graceful deadline: supervisor посылает SIGTERM всем owned process groups, ждёт их параллельно до общего deadline, затем SIGKILL remaining groups и выполняет bounded platform-owned exit proof. Unbounded `process.wait()` отсутствует. Kubernetes `terminationGracePeriodSeconds` выводится из этого deadline плюс documented supervisor cleanup margin.

`health_poll_interval_seconds` не является publisher timeout. Он platform-owned и равен 5 seconds, поскольку это частота cheap loopback health observation. Health polling больше не служит retry delay.

Gateway-mode provider-attempt retry использует platform-owned exponential backoff с baseline initial 1 second и maximum 300 seconds и продолжается indefinitely внутри того же gateway Pod. Validation Job выполняет один bounded lifecycle, возвращает structured transient/deterministic result и всегда освобождает slot после cleanup; WCC ждёт backoff без reservation и заново входит в fair queue. Retry policy не пересоздаёт WorkflowRun.

До finalizing defaults implementation выполняет real measurements exact pinned Gluetun digest на target ARM64 task environment с real `/dev/net/tun` и accepted `/.secret/vpn_config/tr/` snapshot. Acceptance включает repeated cold/warm activation, DNS change, provider network blackhole/recovery, unreachable provider, invalid authentication, internal attempt replacement, graceful stop and forced stop. Final defaults не могут быть ниже baselines; если measured maximum с margin превышает baseline, значение округляется вверх и одинаково фиксируется в stable design, API schema, ORM defaults, runtime and tests before completion.

## Worktree Development Environment

Один task common prefix владеет одной full development environment, общей для всех participating repository worktrees этой task pair. Не создаётся отдельная environment на каждый repository.

Environment machine name вычисляется как `w` плюс первые 15 lowercase hex symbols SHA-256 от exact common prefix. До provisioning controller проверяет, что существующая environment с этим machine name отсутствует либо имеет exact matching full prefix; hash collision fail closed.

Каждый task-specific taggable AWS resource имеет exact tag:

```text
git-worktree=<common-prefix>
```

Tag key `apwid.com/git-worktree`, project tag и project name в AWS identifiers запрещены. `primary` и account-foundation не имеют `git-worktree` tag.

Dedicated account делает project prefix избыточным. AWS names состоят только из resource role и environment identity, например `account-foundation`, `data-primary`, `compute-primary`, `data-w...`, `compute-w...`. Globally unique names добавляют account ID и region. Это относится к stack names, EC2 `Name`, IAM roles, Scheduler groups, log groups, buckets, aliases and other account-local identifiers. Repository/source names и Kubernetes/Product domain identities не являются AWS account resource prefixes и сохраняют собственные domain contracts.

Task environment независимо владеет data/compute stacks, S3/Data/Secret state, Glue/Athena catalog, IAM identities, VPC, EC2, retained EBS, k3s, registry, Product DB, release pointers, tunnel and lifecycle controller. Shared остаются только account, region and account-foundation guards.

Task environments не получают scheduled AWS Backup. They retain only one current task volume; one-time copy snapshots удаляются после copy proof.

`development_environment_manage.py` принимает exact `--git-worktree <common-prefix>` для task operations и сам выводит environment machine name. Task checkout никогда не fallback-ится на `primary`. Source resolver использует same-branch worktrees для participating Product sources, а unchanged repositories — только clean/pushed `main`; release manifest сохраняет exact repository URL and commit for each source.

Primary local HTTP tunnel остаётся `8080`. Task environment получает deterministic collision-checked local port, persisted in environment state. WCC UI automation and manual acceptance use exact environment endpoint; hardcoded task port `8080` запрещён.

Task environment создаётся lazy при первом apply/deploy, имеет ordinary auto-stop и удаляется только explicit cleanup operation.

## Goal Coordination, Checkpoints И YAML

Target task directory в `project-goals` равен `2026-08-01-workflow-platform-hardening/` и содержит `spec.md`, `goal.md`, `checkpoint.yaml`. Current ignored `.spec` pair и prepared `project-goals/.worktree/2026-08-01-workflow-platform-hardening` являются единственным self-hosting bootstrap carrier старого preparer. Эта goal до конца использует current physical pair и все шесть task roots из `Task Identity`; новый lifecycle не переводит её на direct-main execution задним числом. Implementation переносит exact current content в tracked central directory и сохраняет physical pair без дальнейших изменений до отдельного explicit `goal-delete`, чтобы active objective никогда не потерял source path. Она не является compatibility input нового provider: после cutover новые goals используют только central directory, а новые project-local `.spec` roots, links и readers запрещены.

`checkpoint.yaml` начинает с `schema_version: 1`, empty `accepted_checkpoint_id: ""` и empty `checkpoint_list`. Каждый later checkpoint имеет monotonic `checkpoint_id` и sorted full `project_list` всех implementation participants. Workspace root равен physical parent canonical checkout `project-goals`; entry содержит только относительно него разрешённый `project_path` и full `git_commit_final`. `project-goals` из self-referential list исключён. Existing checkpoints immutable. Один coordination commit атомарно публикует complete set.

`goal-checkpoint` создаёт closing commits через `git-commit`, доказывает clean/pushed exact task refs, full origin identities и descendant-or-equal relation каждого target к current repository `origin/main` и prior task checkpoints, затем append-ит один complete snapshot. После первого checkpoint task refs append-only; rebase/reset/force-push запрещены. Main drift требует явной integration новым descendant commit в implementation branch, affected re-verification и нового closing commit; checkpoint workflow не изменяет Product source. `goal-merge` работает в отдельном exclusive thread, до mutation удерживает non-blocking OS lock в canonical `project-goals` Git common directory, preflight-ит fast-forward feasibility всего selected set, затем compare-and-swap fast-forward updates ровно одного selected checkpoint и full primary-environment acceptance. Current manual workflow имеет один operator workspace и не заявляет distributed multi-host locking. Predictable divergence до mutation блокирует весь set; concurrent remote change после preflight сохраняет partial state для resumable failure. Rollback/history rewrite отсутствуют. После failed acceptance pointer не меняется; отдельно согласованный later full fix-forward checkpoint может supersede failed checkpoint только когда per-project ancestry доказывает включение всех intervening commits. Accepted pointer изменяется только после полной зелёной acceptance selected snapshot.

Все mutating lifecycle skills используют task-specific non-blocking OS lock в canonical `project-goals` Git common directory. Любое future изменение checkout либо ref `project-goals/main` дополнительно получает краткоживущий workspace-global coordination write lock; transaction удерживает его от проверки clean synchronized `main` до successful commit/push и clean synchronized return. `goal-merge` отдельно удерживает один workspace-global merge lock и получает coordination write lock только для короткой publication transaction. Seal/revise, checkpoint, merge и delete одной task не выполняются одновременно. Process-local lock не заменяет durable operation journal: после interruption следующий invocation сначала resume-ит либо fail-closed завершает recorded transaction.

Shared `Machine-Readable Format Contract` применяется ко всему change-set. Root `worktree-bootstrap.toml` в пяти implementation repositories заменяется единственным `worktree-bootstrap.yaml`; final `project-goals/main` не содержит bootstrap manifest. WCC `deploy/k8s/overlays/development/workflow-registry/bootstrap/config-template.yml` становится `config-template.yaml`. CloudFormation, Kubernetes and Helm YAML сохраняются. `pyproject.toml`, provider-owned `checker.toml`, package/browser JSON, generated/fixture JSON и external trust JSON не преобразуются, поскольку их format принадлежит соответствующему tool, ecosystem или protocol.

## Synchronized Goal Cleanup

Goal completion сам по себе не удаляет task artifacts или resources. Удаление exact task требует explicit user request и выполняется only through `agent-workflows:goal-delete`; raw `rm` не является supported lifecycle operation. Skill останавливается, если current harness goal ещё unfinished и bind-ит эту task, но не требует retrievable historical goal record, thread id или отдельный completion evidence artifact.

Root `worktree-bootstrap.yaml` получает optional closed cleanup declaration. Declaration содержит только direct argv list with bounded placeholders and находится в stable project owner. Обычная уже поддерживаемая declaration bind-ится до seal. Эта task pair является self-hosting schema-v2 upgrade: current schema-v1 TOML preparer не может принять target YAML schema, поэтому exact target declaration ниже является sealed input, а после реализации parser active validation bind-ит её ровно один раз во все private-state replicas и атомарно создаёт content-free binding receipt в Git common directory каждого participating repository до первой task-scoped AWS mutation. Receipt содержит только schema version, exact task identity, sealed-specification hash, normalized declaration hash, manifest fingerprint и provider-state generation. `development_environment_manage.py apply/deploy --git-worktree` проверяет exact receipt своего repository без parsing общего provider private state и fail closed при absent/malformed/mismatched/stale binding; будущие goals используют ordinary seal-time path. Shared goal lifecycle:

- не использует shell evaluation;
- не содержит project names, AWS profile, workspace root или concrete cleanup command;
- record-ит exact existing declaration and fingerprint before sealing, либо для этого one-time self-hosting upgrade выполняет described pre-resource binding exact sealed declaration;
- при cleanup требует identical declaration in merged/pushed clean `main`;
- выполняет hook из exact main repository root with sanitized environment и передаёт один closed schema-v1 JSON stdin request с exact common prefix и journaled random operation identity;
- принимает success только по closed machine-readable result, который возвращает ту же schema/common-prefix/operation identity и external absence proof.

Для этой task pair sealed exact normalized target `workflow-infrastructure/worktree-bootstrap.yaml` равен:

```yaml
schema_version: 2
resource:
  copy_optional_path_list: []
  copy_required_path_list: []
  link_optional_path_list: []
  link_required_path_list: []
cleanup:
  command_argument_list:
    - python
    - development_environment_manage.py
    - destroy
    - --git-worktree
    - "{common_prefix}"
```

Root manifests `agent-plugins`, `project-standards`, `workflow-control-center` и `vpn-runtime` в той же task change-set переходят на `schema_version: 2` с empty `resource` lists и без `cleanup`, потому что они не владеют отдельными task-scoped external resources. Только `workflow-infrastructure` объявляет external hook. `project-goals` не имеет target manifest: его temporary schema-v1 TOML существует только в current old-contract worktree, не публикуется и удаляется вместе с worktree. Parser отвергает duplicate keys, tags, anchors, aliases, merge keys, unknown fields и invalid types.

Cleanup preconditions:

- explicit user request authorizes deletion of this exact task;
- current harness goal, если он доступен и bind-ит эту task, не является unfinished;
- every participating task branch is clean, fully pushed and ancestor of local/upstream `main`;
- local and remote main agree;
- central task directory and private state still identify exact task set;
- no unmerged/unpushed task or unrelated user work would be removed.

Before first destructive action cleanup atomically creates and fsyncs a coordinating Git-common-directory journal containing exact task identity, participating repositories, branches, worktrees, external hooks and current phase. It never stores secrets or file contents.

Cleanup order:

1. validate preconditions and journal transaction;
2. call each exact project-owned external hook;
3. require exact task AWS stacks/resources absent, S3 versions and multipart uploads removed, SSM sessions closed and retained resources deleted;
4. for task-owned KMS delete alias, disable key and prove AWS `PendingDeletion` with minimum allowed waiting period; physical service deletion may finish later and does not require retaining the current task directory;
5. remove exact task worktrees after proving them clean and merged;
6. remove exact remote task refs, then exact local task refs;
7. remove provider-owned temporary excludes that have durable merged replacements;
8. for this one self-hosting task only, remove its exact immutable bootstrap spec/goal pair from the original ignored `.spec` owner and fsync that directory; unrelated historical pairs remain untouched;
9. delete central task directory from current `project-goals/main`, publish that deletion last and retain prior artifacts in Git history;
10. retire cleanup journal only after every phase succeeds.

Any interruption resumes from journal without relying on an already removed worktree or artifact. Existing absence is idempotent success only when journal and recorded identity prove ownership. Unknown tagged resources, branch divergence, missing proof, changed hook, cleanup failure or possible user work stop operation without deleting the central task directory.

External resource deletion uses exact CloudFormation/resource inventory. `git-worktree` tag inventory is an additional leak check, not a blanket deletion selector, because AWS tag propagation and Tagging API coverage are not universal.

## Pre-Production Cutover

Project is not production. Implementation removes old technical branches instead of maintaining dual behavior:

- old AWS stack/resource names with project prefix are destroyed/recreated under current account-local naming;
- old public VPN validation endpoint and resources are deleted;
- pause-agent image, pause-agent resources, freeze APIs and WorkflowRun VPN switch persistence are removed;
- old flat Product release modules and host artifact monolith callers are removed after all imports migrate;
- old embedded Bash bootstrap is removed;
- this exact unstarted task pair is copied with exact content fingerprints into its `project-goals` directory but continues to execute through its old-contract physical pair and six task roots, every future lifecycle uses direct `project-goals/main` without a coordination worktree, every other pre-cutover pair remains inert and untouched, and this bootstrap pair remains readable at its activation path until explicit `goal-delete`;
- every implementation-repository root `worktree-bootstrap.toml` is replaced by `worktree-bootstrap.yaml`, the WCC bootstrap template uses `.yaml` instead of `.yml`, and final `project-goals/main` contains no bootstrap manifest;
- Product database and disposable development state may be recreated under current schema;
- current ZITADEL owner user and required acceptance VPN snapshot are restored through current documented flows as needed.

This cutover does not delete generic database migration code, current schema version fields, generic release rollback, recovery state machines or future migration capability.

## Verification Design

### `agent-plugins`

Run plugin/skill validators, full provider pytest and deterministic behavior-corpus validation. Real temporary-Git tests cover:

- inactive `goal_ready` revision under same identity, participant expansion, repeated semantic cycle and active-state rejection;
- central task-directory creation, direct-main candidate publication without a coordination branch/worktree/manifest, and removal of project-local `.spec` dependency for future goals;
- clean canonical-main preconditions, workspace-global coordination write locking, task-specific lifecycle locking, workspace-global merge locking, same-task mutation exclusion and journal-first interruption resumption;
- ordinary direct-main commit/push, unrelated-task concurrent replay, same-task conflict rejection, crash recovery at each commit/push boundary and clean synchronized checkout on success;
- complete checkpoint snapshots, closing-commit origin proof, exclusive resumable one-checkpoint fast-forward merge, fix-forward failure and accepted-pointer update only after primary acceptance;
- optional cleanup hook parsing and rejection of shell/unknown placeholders;
- one-time self-hosting binding, atomic content-free receipt replication, exact operator verification, stale/mismatched receipt rejection and final cleanup retirement;
- exact sealed declaration fingerprint;
- unfinished-current-goal behavior rejection without historical-goal dependency, plus unmerged/dirty/unpushed task rejection;
- external hook success/failure, missing/malformed/mismatched stdin request rejection and exact result binding;
- durable journal at every interruption boundary;
- idempotent resumption after external cleanup, partial worktree removal, partial remote/local branch deletion and artifact deletion;
- preservation of unrelated worktrees, branches, main changes and artifacts;
- central task directory deleted last; this self-hosting task also removes its immutable bootstrap pair in the final artifact phase immediately before that central deletion;
- absence of project-specific cleanup knowledge.

Skill behavior evaluation must distinguish `goal-brainstorm`, `goal-checkpoint`, `goal-merge` and `goal-delete`, confirm that inactive candidate correction remains the same goal, and confirm that ordinary goal completion retains artifacts. Pre-cutover corpus invariants that require a coordinating `.spec` owner or `project-goals` worktree are replaced by central direct-main identity and transaction invariants; this task-specific bootstrap exception is not retained as future skill output.

### `project-goals`

Semantically audit `AGENTS.md`, `DESIGN.md` and `README.md` as one owner set. Verify that canonical `main` is the only checkout contract and that no target `.worktree/`, task branch, bootstrap manifest, or self-checkpoint remains. Strict YAML behavior tests cover closed schema, empty-string no-accepted state, append-only monotonic checkpoints, complete sorted project snapshots, duplicate/absolute/escaping path rejection, full commit identities, compare-and-swap conflicts, accepted-pointer ordering and task-directory-last deletion while Git history retains prior artifacts.

### `project-standards`

Run plugin/skill validators, full provider pytest and deterministic behavior-corpus validation. Semantically verify that `Machine-Readable Format Contract` is the single cross-project format-selection owner, YAML uses `.yaml`, JSON/TOML exceptions remain ecosystem-owned and no consumer copies the standard.

### `workflow-infrastructure`

Run full Python tests, format/static checks, CloudFormation validation, exact change-set inspection and real AWS acceptance. Tests cover:

- deterministic environment hash and collision rejection;
- exact `git-worktree` tag, absence of project tag/name and primary tag omission;
- task/main environment isolation and no primary fallback;
- account-foundation single ownership;
- Session Manager preferences/logging;
- task destroy inventory, retained S3/EBS/KMS handling and idempotent absence proof;
- exact local Git-common-directory cleanup-binding receipt verification before task AWS mutation;
- SSM-agent-only UserData, `NewVersion` Command document, explicit no-drift content/status/default/latest verification, exact numeric version plus system SHA-256 bound into SendCommand, content-addressed download, checksum rejection and Python bootstrap;
- artifact provider package ownership;
- task endpoint port allocation;
- primary-only backup and no task scheduled backup.

Real acceptance creates the one task environment for this common prefix, deploys exact task source commits, uses Session Manager access, verifies Product readiness and finally exercises the same destroy hook in a non-destructive dry-run/inventory mode before eventual explicit `goal-delete`. No production resource is created.

### `workflow-control-center`

Run full applicable backend, ORM, controller, Kubernetes render, Product release/retention and UI verification. Behavior tests cover:

- crash during Product local OCI build with zero registry mutation; crash after durable archive/graph intent and before/after every blob, child-manifest and root-index PUT; ambiguous response digest verification; exact-byte continuation; missing-staging fail closed; complete-graph persistence and root-index deletion, включая identical root digest одновременно rooted другим release, rooted shared child manifest и несколько jointly unrooted graphs без взаимной ложной reachability;
- WorkflowSource crash before every blob/child/root PUT after durable graph intent, exact-byte continuation-or-failure semantics, accepted-graph retention, one-day failed-candidate eligibility, shared rooted child and jointly-unrooted graph deletion without concurrent publication/GC;
- exact deletion of root, runnable and SBOM/provenance attestation manifests before release directory and read-only registry blob GC;
- durable registry-root admission fencing across root snapshot/deletion/GC, concurrent API/deploy/rollback root intents, crash continuation and proof-only reopening without timeout;
- one shared OCI protocol package, strict nested Product package ownership and no flat compatibility imports or duplicate registry/graph implementation;
- `VpnConfigVersion` timing defaults, non-null positive values and immutable API/ORM/runtime propagation;
- S3 nonce validation success, expiry, cleanup, redaction and absence public endpoint/AWS Pod credentials;
- atomic run/initial-execution/Product-runtime snapshot, initial zero/one/multiple VPN admission, all-or-none slots and all-ready barrier;
- transient validation retry releases its attempt slot and re-enters fair queue вместо бессрочного удержания connection capacity;
- provider outage leaving run `created` without finite failure;
- post-start VPN outage/rotation not changing WorkflowRun state/deadline/execution;
- spare/no-spare slot rotation with transactional base/surge ownership transfer, authoritative activation-time DNS lookup, stable proxy Pod-UID/runtime-instance fencing across same-Pod container restart, generation-local egress policy and exact generation switch;
- block/delete dual control-disable/egress-revocation, asynchronous gateway cleanup and early base-slot release without WorkflowRun transition;
- deletion of pause-agent/freeze resources and persistence;
- cleanup of all historical gateway generations and slot release.

Any `ui/**` or user-visible UI change follows the exact WCC handoff order in `workflow-control-center/AGENTS.md`, against the task environment endpoint rather than hardcoded `8080`.

### `vpn-runtime`

Run full pytest, compileall, container build and real `/dev/net/tun` integration. Tests cover:

- separate minimal stable-proxy image contains no gateway packages/capabilities, starts without upstream and rejects traffic;
- generation-fenced disable/set/status;
- atomic ready upstream switch, Pod-name race rejection and same-Pod container-restart instance invalidation;
- config/credential isolation and NetworkPolicy-facing address boundary;
- one total connection deadline;
- system DNS re-resolution per provider attempt;
- Gluetun internal recovery grace then same-Pod attempt replacement;
- independent health poll and exponential retry delay;
- parallel graceful process termination, SIGKILL fallback and bounded exit proof;
- no `observed_exit_ip`;
- no browser/WorkflowRun imports or state.

Real measurements use exact pinned Gluetun digest and target ARM64/k3s environment. Secret bytes, presigned URLs, VPN credentials and exact config content never enter logs or artifacts.

### Semantic И Terminal Acceptance

Reread every changed stable owner as one contract set. Confirm single ownership, complete state/failure transitions, no rejected alternative, no compatibility bridge, no duplicate environment, no production mutation and no durable rule owned only by this specification.

After implementation appears complete, perform the goal-brainstorm Terminal Completion Audit from scratch over current repositories and owned development-account state. Fix every finding, rerun affected verification and restart a complete audit. Goal completes only after a fresh post-fix full audit has zero findings and all required checks remain green. Separate evidence documents are not created.

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
