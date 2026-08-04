# Укрепление Архитектуры Сред `Workflow Control Center`

## Назначение

Эта спецификация владеет cross-repository укреплением development-среды `Workflow Control Center`: единым разрешением moving platform dependency на границе Product release, разделением environment-neutral Product implementation и adapters сред, устранением хрупких Kubernetes/AWS решений, destructive pre-production cutover на единственные текущие технические contracts и фиксацией обязательной будущей production-архитектуры на Amazon EKS.

Устойчивая общая архитектура принадлежит `workflow-infrastructure/design/environment-model.md`, development specialization — `design/development-environment.md`, future production specialization — `design/production-environment.md`. Product runtime, Data и UI contracts принадлежат соответствующим design-документам `workflow-control-center`. Эта спецификация владеет только scope реализации, согласованным pre-production cutover и общей acceptance нескольких владельцев.

## Проверенное Исходное Состояние

Development environment уже работает в выделенном AWS account:

- account ID `463564115167`;
- account name `workflow-control-center-devel`;
- region `us-east-1`;
- local SSO profile `workflow-control-center-devel`;
- один EC2 `m7g.xlarge` с single-node k3s;
- standing authority пользователя на необходимые изменения внутри этого account без отдельной approval pause.

Account `227373271916` является AWS Organizations management account, а не WCC production account. Future production account не назначен.

Product release уже сохраняет exact local repository URLs, commit SHAs, archive digests, file hashes, image digests и Kubernetes render. Recovery проверяет retained source graph и не пересобирает images. При этом `workflow-container-contract` ошибочно классифицирован как обычный обязательный sibling checkout, а platform recovery одновременно принимает несколько прежних manifest shapes и содержит одноразовые compatibility branches для старого host/runtime state.

`workflow-control-center/requirements.txt` содержит:

```text
workflow-container-contract @ git+https://github.com/antonov-andrey/workflow-container-contract.git@main
```

Backend Docker build сначала устанавливает весь `requirements.txt`, поэтому получает moving branch из сети, а затем устанавливает exact contract из named build context. Это два независимых source path одного package в одном image build. `workflow-container-runtime` platform-base и `browser-runtime` уже принимают exact contract build context и не нуждаются в VCS resolution.

Текущий Kubernetes graph содержит development-specific PostgreSQL, retained registry, storage classes и registry endpoints в `deploy/k8s/base`. Rootless BuildKit работает как один постоянный Deployment/Service для всех publishers. AWS credential JSON монтируется через file `subPath`, поэтому обновление Kubernetes Secret не становится видимым существующему mount стандартным atomic secret-volume mechanism.

Development data-plane template:

- задаёт `DeletionPolicy: Delete` и `UpdateReplacePolicy: Delete` для KMS и buckets;
- удаляет все noncurrent Data/Secret versions через blanket 30-day S3 Lifecycle независимо от Product references;
- оставляет permanent `apwid-data-access` object permissions на весь Data/Secret bucket и полагается на выдаваемую session policy как единственную prefix boundary;
- имеет bucket-level Block Public Access, но не account-level S3 Block Public Access;
- использует broad `AdministratorAccess` platform role, хотя design ошибочно называл его будущей production policy.

Development registry не задаёт `Recreate` и не имеет точной ingress/egress NetworkPolicy. Host bootstrap скачивает часть third-party artifacts через moving URLs без exact version/digest proof. Kubernetes Secret export archives не имеют обязательного 30-day cleanup. UI nginx правильно разделяет no-store documents и immutable hashed assets, но не отдаёт standard security headers.

## Требуемый Результат

Каждый новый Product release разрешает current remote default-branch `HEAD` `workflow-container-contract` ровно один раз, сохраняет exact immutable source identity и использует один и тот же exported tree во всех platform consumers. Build, rollback, recovery и `WorkflowRun` не разрешают moving source повторно. Новый release с несовместимым latest contract полностью отклоняется без silent fallback.

До нового deploy выполняется destructive pre-production cutover: старые Product databases, S3 object versions/delete markers/multipart uploads, dynamic Glue tables, workflow volumes, registry contents, retained Product releases и Product-tool environments удаляются; ZITADEL и GlitchTip identity/observability state сохраняется на месте и проверяется до и после reset. После cutover platform implementation принимает только текущую technical source-manifest shape, текущую Product release-manifest shape и текущие CLI/runtime contracts. Конкретные readers, writers, sanitizers, aliases и transition branches прежних shapes удаляются. Универсальные механизмы версионирования, миграции, rollback и recovery текущей системы сохраняются и не считаются legacy только потому, что сейчас нет зарегистрированного перехода со старой реализации.

Concrete workflow repositories содержат только текущую source release и текущую input shape; конкретные historical migration entries/scripts и compatibility fields отброшенных releases удаляются. При этом schema и loader универсального migration graph, механизм исполнения явно объявленной миграции, доменная сущность `WorkflowSourceVersion`, immutable build/execution attempts и schema versions queryable datasets сохраняются как части текущей реализации. Старый workflow не поддерживается не потому, что миграции в принципе запрещены, а потому, что для него больше нет declaration или отдельной code branch.

Persisted `WorkflowSource.interface_major_version`, source-version build snapshots и `WorkflowRun.interface_major_version` принимают только exact current shared interface major. API, ORM и runtime spec не расходятся и не используют правило «любое положительное major».

Development продолжает работать как одна полная environment `primary` на одном EC2/k3s instance. Product source, Kubernetes base, release graph и data-plane contract становятся environment-neutral; development-specific registry, PostgreSQL, storage, ingress, identity и lifecycle живут только в development adapter. Вторая environment и production resources не создаются.

Future production architecture становится однозначной: dedicated account, Amazon EKS multi-AZ, EKS Pod Identity per trusted workload, ECR, RDS, EBS CSI, TLS ingress, HA/disruption contracts и отдельная sandbox RuntimeClass/node boundary для arbitrary untrusted images. Development `AdministratorAccess`, k3s, local registry, local PostgreSQL, local-path storage, SSM HTTP tunnel и auto-stop не являются production defaults.

AWS/Kubernetes hardening устраняет credential refresh bug, shared builder daemon, unsafe state deletion policies, tenant-role overbreadth, registry race/network ambiguity, unverified host downloads, indefinite secret archives и отсутствующие UI security headers. Полная development Product/recovery acceptance остаётся зелёной.

## Post-Completion Architecture Audit Corrections

Повторный архитектурный аудит расширяет обязательный final state следующими
исправлениями:

- platform-owned images разрешают base selectors один раз на Product release,
  используют hash-locked dependencies, сохраняют exact base graph, Buildx metadata,
  maximal provenance и SBOM, а recovery не пересобирает images;
- dynamic Kubernetes workloads разделены на platform/build/data/runtime/VPN trust
  domains и защищены exact admission, quota, resource, ServiceAccount,
  capability, host-path и network contracts;
- development registry использует reference-aware mark-and-sweep от current
  release, rollback marker, accepted `WorkflowSourceVersion` и
  active/recoverable `WorkflowRun`; registry GC выполняется только после остановки
  writers и доказанного read-only idle safepoint;
- host lifecycle публикует bounded root/retained volume-pressure warnings и
  запускает idle-only Product maintenance;
- два перегруженных orchestration owner разделяются на concrete image,
  retention, identity, artifact, storage и retained-release collaborators без
  mixins, pass-through layers или скрытого dependency construction; общий
  `python-developer` contract запрещает добавлять вторую independent
  responsibility family в orchestration owner;
- retained EBS основного development server защищает primary-only AWS Backup plan
  с семидневным lifecycle и exact current-volume ARN selection. Backups являются
  crash-consistent; дополнительные development servers не получают регулярных
  backups, а одноразовые copy snapshots удаляются после копирования. Это только
  development policy;
- account-level S3 Block Public Access и Lake Formation settings имеют одного
  owner — `primary` data-plane stack; другие environments не создают competing
  resources и только проверяют exact state до/после apply;
- idle decision использует только наличие Session Manager sessions и Product
  activity. Content-level audit port-forwarding/SSH-over-SSM не требуется и не
  заявляется.

Текущий VPN freeze flow не меняется без отдельного решения пользователя. Audit
обязан объяснить его фактическое назначение и сравнить с prepared ready gateway
плюс stable Service selector cutover, не выдавая `SIGSTOP` за сохранение TCP.

## Scope И Non-Goals

В scope входят:

- typed moving-source resolution и immutable Product release provenance;
- удаление moving contract dependency из WCC runtime requirements;
- single-source contract installation во всех platform-owned images;
- common/development/production environment documentation and owner routing;
- parameterized development environment identity с default `primary`;
- environment-neutral Kubernetes base и development adapters;
- disposable rootless BuildKit Pod per source-build attempt;
- directory-mounted renewable AWS credential Secret;
- retained/local registry `Recreate` и exact NetworkPolicy;
- reference-aware S3 lifecycle, state-resource `Retain`, account-level S3 Block Public Access и tagged permanent tenant policy;
- verified host artifact versions/digests;
- 30-day development Kubernetes Secret archive cleanup;
- standard UI HTTP security headers;
- deterministic, integration, real AWS и remote Product acceptance;
- destructive reset старого development Product/workflow state и удаление technical legacy compatibility;
- удаление конкретных завершённых либо неиспользуемых migration implementations и one-time ZITADEL/GlitchTip dump-import contour без удаления универсального migration framework;
- логические commits и push всех затронутых repositories.

В scope не входят:

- создание либо изменение production AWS account, EKS, ECR, RDS, DNS или public endpoint;
- создание второй development environment;
- разделение текущего development EC2 на несколько instances;
- production sandbox implementation или выбор конкретной технологии gVisor/Kata;
- изменение `WorkflowSourceInterface` либо принудительное использование Python/base image сторонним publisher;
- новая конкретная database/workflow-input migration для уже удалённого pre-cutover state, dual deployment или parallel local Kubernetes branch;
- удаление либо переписывание универсальных migration/versioning mechanisms текущей системы;
- новый billing/chargeback;
- multi-architecture OCI publication;
- hardcoded contract commit в каждом repository, requirements file или Dockerfile;
- automatic fallback с неработающего latest contract на предыдущий.

## Владение Репозиториями

### `workflow-infrastructure`

Репозиторий владеет:

- общей моделью сред и adapters;
- moving-source declaration/resolver;
- immutable source graph и retained release validation;
- development environment identity и operator interface;
- development CloudFormation data/compute planes;
- host bootstrap artifact resolution;
- AWS change sets, live isolation и recovery acceptance.

`.spec/2026-07-28-workflow-development-environment-spec.md` и paired goal остаются неизменёнными. Новая pair дополняет завершённую исходную цель и не переписывает её историю.

### `workflow-control-center`

Репозиторий владеет:

- runtime dependency composition и backend image;
- environment-neutral Kubernetes Product base;
- development Kubernetes overlay;
- source-build worker и disposable builder lifecycle;
- credential volume mounts;
- registry deployment/NetworkPolicy;
- Product Data reconciler and policy assumptions;
- UI nginx policy и browser acceptance;
- Product release manifest, images, render и recovery checks.

### `workflow-container-contract`, `workflow-container-runtime` И `brand-size-chart`

`workflow-container-contract` владеет единственной текущей формой `workflow.yaml` и `versions.yaml`. Current schema и loader поддерживают явно объявленный version-to-version migration graph, но repository не содержит declarations, aliases или parsers конкретных отброшенных legacy shapes.

`workflow-container-runtime` исполняет одну текущую concrete DBOS workflow implementation. Универсальные DBOS/version/recovery mechanisms сохраняются; отдельная ветка исполнения старой concrete workflow implementation отсутствует.

`brand-size-chart` владеет одним текущим source release. Historical release prose, declarations и migration scripts для удалённых releases отсутствуют, но current contract по-прежнему позволяет добавить будущую явную migration без нового platform mechanism.

### `agent-plugins`

`workflow-container-agent-tools:workflow-container-input-create`, developer routing и audit vocabulary используют только current contract implementation. Они не содержат hardcoded legacy field transfer; если source объявляет migration по current schema, используется общий source-owned migration mechanism.

### Verification Consumers

`workflow-container-runtime` и `browser-runtime` участвуют в exact-source/image compatibility verification. Они меняются только если проверка доказывает нарушение утверждённого contract; отдельные moving selectors или consumer-local exact pins в них не добавляются.

`vpn-runtime` и `brand-size-chart` участвуют только в complete WorkflowRun acceptance и меняются лишь при доказанном defect.

## Product Release Source Graph

### Source Kinds

Source graph является typed union:

- `exact_checkout`: clean local repository с exact upstream-published HEAD;
- `resolved_moving_source`: repository URL и moving selector, разрешённые resolver в exact exported tree.

`workflow-infrastructure`, `workflow-control-center`, `workflow-container-runtime`, `browser-runtime` и `vpn-runtime` остаются `exact_checkout`. `workflow-container-contract` становится `resolved_moving_source` со standard selector `HEAD` advertised remote default branch. Локальная копия contract может использоваться для разработки, но не определяет Product release dependency.

### Resolution

Resolver работает на trusted operator boundary до source transfer и первого image build:

1. получает `git ls-remote --symref` identity `HEAD`;
2. fetch-ит exact advertised ref в disposable temporary repository;
3. повторно проверяет ref/commit pair и при race отбрасывает candidate целиком, затем выполняет bounded fresh resolution;
4. экспортирует только exact tracked tree без `.git`, credentials, untracked files и undeclared submodules;
5. вычисляет deterministic archive SHA-256 и SHA-256 каждого path;
6. читает package version из exact tree без исполнения source;
7. сохраняет immutable source entry.

Source entry содержит:

- `source_kind`;
- `repository_url`;
- `requested_selector`;
- `resolved_ref`;
- `commit_sha`;
- `package_version`;
- `archive_sha256`;
- `file_sha256_by_path_map`;
- declared submodule graph, который для contract обязан быть пустым;
- explicit override identity и reason, если override был разрешён пользователем.

Разрешение выполняется один раз на release candidate. Все consumers получают ссылку на один source entry и один staged directory. Повторный fetch ради отдельного image запрещён.

### Exact Override

Operator CLI может принять exact contract override только через явный одноразовый argument текущего deploy. Override не хранится как новый default и не подхватывается из environment variable. Resolver доказывает, что commit принадлежит configured repository, экспортирует его тем же способом и помечает release manifest как explicit override.

Обычный следующий deploy снова использует current remote `HEAD`. Failure latest не предлагает и не выбирает override автоматически.

### Единственная Текущая Manifest Version

Source manifest обязан иметь exact current `source_manifest_version`, environment identity, host-artifact identity, запрет bytecode writes и полный typed source graph. Product release manifest обязан иметь exact current `release_manifest_version`, source/render digests, UI security policy, image provenance и environment identity. Отсутствующая либо любая другая version является error.

Recovery читает только текущую manifest shape, никогда не выполняет Git access, package resolution, cleanup либо преобразование retained source bytes. Previous immutable Product releases могут участвовать в rollback, если они уже созданы текущей implementation и текущей manifest shape; это штатный механизм текущей системы, а не legacy compatibility.

## Dependency И Image Contract

`workflow-control-center/requirements.txt` больше не содержит `workflow-container-contract` VCS requirement. Runtime dependencies, устанавливаемые из package index, остаются отделены от exact release-owned source.

Backend Dockerfile:

- сначала устанавливает ordinary `requirements.txt`;
- затем ровно один раз устанавливает exact contract named build context;
- не содержит repository URL, branch, tag или commit contract;
- не имеет network access к Git для contract;
- проверяет установленную package version/source identity в build acceptance.

Local developer/test bootstrap устанавливает contract из sibling checkout, предварительно проверенного по exact configured repository URL; это development-only source. Он не возвращает moving VCS dependency в runtime requirements и не создаёт commit pin в `requirements-dev.txt`.

`workflow-container-runtime` platform base и `browser-runtime` используют тот же named context exact tree. Product release manifest сохраняет contract source identity рядом с каждым consuming image digest, а acceptance доказывает равенство identity, package version и bytes.

## Environment Model

### Common Product Boundary

`deploy/k8s/base` содержит только environment-neutral Product resources и interfaces. Он не содержит:

- local PostgreSQL Deployment/PVC/bootstrap;
- retained local OCI registry Deployment/PVC/NodePort;
- `apwid-development-static`, local-path host directories или development storage class;
- SSM/local ingress origins;
- physical AWS account, bucket, role, KMS или registry names;
- development-only lifecycle controller.

Stable service/config interfaces могут оставаться common, но их physical implementation/value приходит только из environment adapter. Render без adapter fail-closed не считается deployable environment.

### Development Adapter

`deploy/k8s/overlays/development` владеет:

- local retained PostgreSQL and bootstrap;
- retained cluster-local OCI registry and NodePort used by host build;
- local-path and static retained storage;
- ingress-nginx HTTP origins through SSM;
- EC2 credential-process source;
- k3s resource tuning;
- development-only state/recovery resources.

Source files не дублируют common workloads. Kustomize patches меняют только approved adapter surfaces.

### Environment Identity

Operator и templates принимают stable `environment_name` с default `primary`. Machine value имеет один documented safe pattern. Для `primary` stack names, logical IDs, existing physical resource names, retained paths и commands остаются точными текущими identities.

Любое другое name детерминированно формирует отдельные data-plane/compute stack names, physical names, retained root, release root, registry, Product DB, credentials, SSM target и lifecycle identity. Тесты доказывают отсутствие collision. Current goal только реализует parameterization и `primary`; никакие дополнительные AWS resources не создаются.

### Production Contract

Production implementation не создаётся, но static design и common-base tests фиксируют:

- dedicated, пока не назначенный AWS account;
- Amazon EKS multi-AZ managed node groups;
- EKS Pod Identity per trusted workload без Pod `AdministratorAccess`;
- Amazon ECR, RDS Multi-AZ/PITR и EBS CSI;
- TLS ingress/DNS;
- HA, topology, disruption, rollout and autoscaling;
- separation trusted/untrusted node groups;
- mandatory approved sandbox RuntimeClass для arbitrary user images.

Production adapter обязан использовать common Product base и data-plane contract. Копия backend/UI/controller/runtime tree запрещена.

## Kubernetes Security Hardening

### Renewable AWS Credentials

Common trusted AWS consumer использует standard provider chain и default `ambient` adapter без credential volume. Development environment явно выбирает `credential_process`: только этот adapter монтирует secret volume directory в `/var/run/workflow-control-center/aws` без file `subPath`, а `credential_process` читает `credentials.json` из этой directory. Secret `items` ограничивает volume exact required key. Future production сохраняет common `ambient` adapter и использует EKS Pod Identity.

Atomic Secret update становится видимым существующему development Pod через Kubernetes secret-volume projection. Development readiness проверяет новое expiration/identity до expiry старой session. Dynamic run-local control/materialization Pods используют тот же explicit adapter; workflow, browser, VPN, validation и wait Pods его не получают.

Каждая trusted AWS workload family имеет отдельные stable ServiceAccount и owner-local ConfigMap/Secret interfaces, пригодные для отдельных production Pod Identity и RDS roles. Credentialed control/materialization ServiceAccount или DB Secret не используется workflow, browser, VPN provider, source builder/validation, wait либо другой workload family.

Live acceptance обновляет renewable credential, доказывает чтение новой session тем же Pod UID без restart и не печатает credential fields.

### Disposable BuildKit

Постоянные `workflow-builder` Deployment и Service удаляются. Одна durable WorkflowSource build attempt владеет одним deterministic disposable rootless BuildKit Pod:

- exact attempt labels/name и adopted exact Pod UID;
- `automountServiceAccountToken: false`;
- no Docker socket, host mount, privileged mode, platform credential, Product DB, Data или runtime Secret;
- attempt-local `emptyDir` cache/tmp;
- exact digest-pinned BuildKit image;
- explicit requests/limits and a Pod-level `RuntimeDefault` baseline; only the
  rootless BuildKit container receives the proved narrow runtime exception:
  `Unconfined` seccomp/AppArmor, `allowPrivilegeEscalation: true` and
  `SETUID`/`SETGID` after `drop: ALL`, while remaining non-root, read-only,
  credentialless, unprivileged and attempt-local;
- ingress только от exact build-worker identity;
- egress только DNS, public dependency endpoints с явным исключением metadata/VPC/cluster/service/Product network ranges и exact read-only registry proxy;
- deletion after accepted success/failure/cancellation.

Worker crash повторно принимает exact owned Pod либо создаёт replacement only after proof отсутствия previous Pod. Concurrent builds разных users получают разные Pods/caches. One user Dockerfile cannot observe processes/files/cache of another attempt.

BuildKit может получать optional platform base image только через stateless registry proxy, разрешающий `GET`/`HEAD` exact platform-base repository и запрещающий private user repositories и write/delete methods. Он экспортирует OCI artifact в attempt-local worker-owned path и не имеет network route к writer registry. Trusted build-worker push boundary проверяет manifest/platform/digest и только затем публикует immutable image. Registry credential и delete permission существуют только в этой trusted boundary. Builder never receives Git/registry credentials. Build logs сохраняют current redaction/provenance.

### Development Registry

Registry resources принадлежат development overlay. Deployment задаёт `replicas: 1` и strategy `Recreate`; две registry replicas никогда не используют один retained filesystem одновременно.

Image pull выполняет node/containerd, а не network identity целевого Pod. Поэтому writer-registry NetworkPolicy разрешает trusted k3s node boundary для kubelet pull и host Product-image build, exact trusted source-build push/cleanup workload, stateless read-only proxy и required health path. Untrusted builder обращается только к proxy. Proxy NetworkPolicy принимает только exact builder identity и ходит только в writer registry; nginx path/method policy разрешает registry `GET`/`HEAD` только exact platform-base repository, запрещает private repositories, request body и все mutation methods. HTTP method authorization не имитируется L3/L4 policy.

Policy проверяется фактическим pull platform base через proxy, denied push/delete через proxy, trusted publication, kubelet pull и denied writer access из другого namespace. Production common base registry Deployment не содержит.

## AWS Data-Plane Hardening

### Retention И Deletion

Storage KMS key и Data, Secret, Result, Observability buckets получают `DeletionPolicy: Retain` и `UpdateReplacePolicy: Retain`. CloudFormation stack delete/replacement не удаляет customer Data или ключ дешифрования. Намеренный pre-production reset остаётся отдельной exact-owned operation.

Blanket Data/Secret `NoncurrentVersionExpirationInDays` удаляется. Остаются только:

- abort incomplete multipart staging через один день;
- completed `data-download/` expiration через два дня;
- `athena-result/` expiration через 30 дней;
- `source-map/` expiration через 30 дней.

Product reference-aware reconciler остаётся единственным владельцем 24-hour orphan cleanup и 30-day Data/Secret historical purge. Tests доказывают, что referenced version переживает 30 дней и удаляется только после release всех references и retention.

### Tenant IAM

Permanent `apwid-data-access` role policy использует mandatory `aws:PrincipalTag/UserDataRootId` для всех возможных tenant object/list prefixes:

- canonical Data;
- canonical Secret;
- operation staging;
- owner Result downloads and query outputs.

`ListBucket`/`ListBucketVersions` имеют exact prefix conditions. Object resources используют tag-derived ARN patterns. Missing/malformed/mismatched tag не разрешает bucket-wide list/read/write.

Backend-generated session policy остаётся обязательным вторым restriction и только сужает operation, actions и exact path. Она не компенсирует broad permanent role.

Real AWS acceptance двумя users проверяет canonical, staging, Secret, Result, current/version/list/multipart и denied cross-user paths.

### Public Access И Environment Authority

Data-plane stack создаёт account-level S3 Block Public Access со всеми четырьмя flags и сохраняет bucket-level controls/TLS policies. Account-level guard не удаляется обычным stack cleanup.

Development broad platform role остаётся explicit isolated-account exception. Design, code comments и outputs не называют её production-equivalent. Future production workloads используют separate least-privilege Pod Identity roles.

CloudFormation source имеет одного common data-plane owner and parameterized environment values. Existing development stack name, logical IDs и physical resources `primary` сохраняются. Change set с replacement либо deletion state-bearing resource отклоняется.

## Host Bootstrap Supply Chain

Third-party bootstrap artifact имеет source selector and exact resolved identity:

- AWS CLI v2 archive;
- Docker repository signing key and exact package versions;
- `uv` binary/release artifact;
- Python selector `3.14`, resolved for one compute release to exact available patch/build;
- k3s install script and binary version;
- Helm archive.

Source keeps supported moving line such as Python `3.14`, not a patch hardcode repeated across files. Before compute change set the orchestrator resolves it once, records exact version plus SHA-256/signature in immutable launch/release input and verifies bytes before execution. Replacement from the same launch-template version uses the same exact identities.

Unversioned installer URL, unchecked GPG key, `curl | sh`, package-manager latest third-party package and silent fallback are prohibited. Ordinary Ubuntu base packages come from the exact selected AMI repositories without `apt upgrade`; this exception does not authorize moving third-party install scripts.

## Development Secret Archive

Retained Kubernetes Secret export keeps exact current file and timestamped previous archives only. After successful atomic current replacement, controlled cleanup removes archive files older than 30 days by UTC creation identity. The same cleanup runs from the 15-minute credential-refresh timer even when current export bytes did not change. Cleanup:

- never follows symlinks;
- never removes current;
- validates filename/path ownership;
- is idempotent and controlled-clock testable;
- never logs content;
- preserves any exact artifact still explicitly required by current recovery state.

## UI HTTP Security

One central generator produces nginx security policy from validated exact Product/ZITADEL/GlitchTip/S3 origins and places its digest in Product release inputs. Page/local code and environment overlay do not hand-write alternate headers.

All document and asset responses contain:

- CSP with `default-src 'none'`, `base-uri 'none'`, `object-src 'none'`, `frame-ancestors 'none'`, self-only scripts without `unsafe-eval`, and exact allowlisted connect/image/font/worker/frame/form origins;
- `X-Content-Type-Options: nosniff`;
- `Referrer-Policy: no-referrer`;
- `X-Frame-Options: DENY`;
- `Permissions-Policy: camera=(), display-capture=(), geolocation=(), microphone=(), payment=(), usb=()`.

Inline scripts are absent. Central `style-src 'unsafe-inline'` is allowed only for the current shared UI library requirement. HSTS is absent on development HTTP and required only at future production HTTPS boundary.

Existing cache contract remains: no-store document/SPA fallback and immutable content-hashed assets. Browser acceptance proves login, silent renewal, GlitchTip, direct S3, PDF worker, current-assets reload and error boundary under exact CSP.

## Destructive Pre-Production Cutover

Project остаётся pre-production, поэтому existing Product/workflow state в этом cutover не мигрируется и не читается новой реализацией. Это позволяет удалить конкретные старые migrations и compatibility branches, но не разрешает удалять generic database/workflow-input migration mechanisms текущей системы.

Перед cutover выполняется exact inventory. Existing selective Product reset сохраняет на месте ZITADEL users, role grants, GlitchTip database/uploads/source maps и retained root identities, но удаляет Product databases, все Data/Secret/Result object versions и multipart uploads, dynamic Glue tables, workflow namespace/PVs, workflow-run storage и registry contents. Затем удаляются retained Product release/current/recovery-marker и Product-tool runtime contents. Старые concrete workflow source artifacts, конкретные obsolete migration declarations/scripts, завершённый one-time identity dump-import contour и legacy branches удаляются из Git; reusable migration verifier/loader остаётся.

CloudFormation hardening может обновить current development resources in place, но код не содержит переходной ветки для старого stack/runtime/manifest. Если live infrastructure не удовлетворяет current template, выполняется обычный approved pre-production reset/recreate, а не compatibility bridge. Production resources, вторая development environment, local cluster и duplicate implementation не создаются.

Первый новый deploy создаёт Product release и workflow sources только через current implementation. Subsequent rollback/recovery между releases той же current manifest shape остаётся штатной функцией; код старой implementation для этого не сохраняется.

## Verification

### Deterministic And Contract Verification

`workflow-infrastructure` tests cover:

- moving default-branch discovery, resolution race, deterministic export and exact override;
- one resolution per release and one source identity across consumers;
- strict rejection of absent, previous, and unknown source/Product manifest versions;
- absence of legacy runtime, pre-hardening stack, source sanitation and compatibility branches;
- no Git/network access during recovery;
- environment-name validation, `primary` identity preservation and non-primary collision isolation;
- CloudFormation Retain/lifecycle/account-block/tag-policy contracts;
- exact host artifact version/digest resolution;
- Secret archive 30-day cleanup;
- common/development/production document owner consistency.

`workflow-control-center` tests cover:

- absence VCS contract dependency in runtime requirements;
- single exact package installation in backend image;
- environment-neutral base and complete development render;
- no credential file `subPath` and reusable directory mount;
- disposable BuildKit Pod ownership, isolation, adoption and cleanup;
- registry `Recreate`/NetworkPolicy;
- exact UI security headers/CSP and unchanged caching.

`workflow-container-contract` and concrete workflow tests cover:

- one current `versions.yaml` schema and one generic migration loader without legacy parser/alias branches;
- absence of concrete migration declarations/scripts for removed workflow releases while future explicit migrations remain supported;
- preservation of generic platform DB migration tooling and absence of completed one-time identity dump-import branches;
- `brand-size-chart` current input and workflow contracts without historical fallbacks.

Applicable full Python, TypeScript, manifest, code-contract, CloudFormation and semantic suites run according to each repository `AGENTS.md`.

### AWS And Kubernetes Acceptance

Before AWS mutation:

- prove caller account `463564115167`, region `us-east-1`, profile and exact stacks;
- inspect live parameters, outputs, drift and current retained resource identities;
- run `cfn-lint` and `aws cloudformation validate-template`;
- create and inspect exact change sets;
- prove projected recurring cost delta remains within approved architecture and does not create a second environment;
- inventory every Product state owner that the approved pre-production reset removes and every ZITADEL/GlitchTip owner it preserves.

After reset and apply:

- prove no state-bearing physical identity changed;
- prove account/bucket public-access blocks and Retain policies;
- run real two-user tenant isolation including direct S3 and Athena;
- rotate EC2 credential-process Secret and prove same Pod UID uses renewed session;
- run concurrent source builds and prove unique builder Pods/cache plus cleanup on success/failure;
- prove registry allowed/denied traffic;
- run one `WorkflowSourceVersion` build and one `WorkflowRun` using exact proxy/runtime path;
- prove new release manifest resolves current remote contract once and every consumer image uses it;
- prove rollback and retained recovery between releases created by current implementation use exact source without Git;
- prove no previous manifest/runtime/workflow-input implementation, concrete-workflow legacy declaration/script or compatibility branch remains in Git or live Product state;
- prove generic versioning/migration/rollback/recovery mechanisms remain usable and contain no hardcoded knowledge of removed legacy versions.

### UI Acceptance

Любое изменение `ui/**` либо user-visible UI behavior выполняет exact current sequence из `workflow-control-center/AGENTS.md`: tests, build, clean candidate commit/push, remote development deploy, SSM tunnel, automated browser suite и complete manual workflow against current deployed assets.

HTTP inspection и browser tests проверяют headers на document, SPA fallback, hashed JS/CSS, worker and error responses. OIDC, GlitchTip, S3 and PDF remain functional under CSP.

### Terminal Semantic Audit

После предполагаемого завершения выполняется полный независимый semantic audit:

- каждая requirement этой specification сопоставлена exact owner/code/test/live evidence;
- common/development/production prose проверена на duplication и false equivalence;
- requirements/Dockerfiles/release manifests проверены на второй contract source;
- Kubernetes base проверен на development leakage;
- AWS policies проверены по effective access, а не substring tests;
- recovery проверен без network resolution;
- technical manifest and workflow-input compatibility branches отсутствуют, а current domain versioning не ошибочно классифицировано как legacy;
- live Product проверен end to end.

Все findings исправляются, invalidated checks повторяются, затем audit запускается заново. Цикл audit/fix продолжается до одного свежего полного прохода без новых findings и uncovered requirements. Дополнительные evidence-documents не создаются.

## Completion Contract

Завершение включает логические commits, push всех затронутых repositories, clean worktrees и exact local HEAD равный upstream branch. `.spec/2026-07-30-workflow-environment-architecture-hardening-spec.md` и paired goal остаются ignored, untracked и сохранёнными; они не удаляются автоматически.

Успешный unit test, template validation, stack status, Pod readiness либо частичный smoke не заменяет full semantic, AWS, Kubernetes, UI, release and recovery acceptance.
