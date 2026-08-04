# Удалённая Среда Разработки `Workflow Control Center`

## Назначение

Эта спецификация владеет согласованным cross-repository cutover от laptop kind cluster к single-node k3s на EC2 в выделенном AWS account разработки, переносом инфраструктурного владельца из `marketplace-infrastructure` в новый проект `workflow-infrastructure`, переходом на native target-platform builds и полной приёмкой Product, identity, observability, Data/Athena, browser, VPN и workflow runtime.

Устойчивая архитектура принадлежит `workflow-infrastructure` — `DESIGN.md` и `design/development-environment.md`, Product runtime и storage semantics — design-документам `workflow-control-center`, а browser implementation — `browser-runtime` — `DESIGN.md`. Эта спецификация владеет только scope реализации, переходным состоянием, destructive cutover и общей приёмкой нескольких владельцев.

## Проверенное Исходное Состояние

Репозиторий `workflow-infrastructure` создан как `git@github.com:antonov-andrey/workflow-infrastructure.git`, локальный checkout находится в `/home/andrey/Projects/workflow-infrastructure`. До этой цели он не содержит executable infrastructure.

Account разработки:

- account ID `463564115167`;
- name `workflow-control-center-devel`;
- region `us-east-1`;
- local SSO profile `workflow-control-center-devel`;
- Organizations OU `Sandbox`;
- Identity Center group `WorkflowControlCenterDevelopmentAdministrators`;
- permission set `AdministratorAccess`;
- standing authorization пользователя на любые необходимые изменения внутри этого account без отдельной паузы.

Live data-plane stack `workflow-control-center-development` имеет status `UPDATE_COMPLETE`. Последняя зафиксированная drift-проверка `22093c80-8612-11f1-adda-0affc718b2a5` завершилась `IN_SYNC` с нулём drifted resources. Stack уже владеет S3, KMS, IAM, Glue, Lake Formation, Athena, API Gateway и Observability bucket.

Его template, отдельный template Budget и account design сейчас физически находятся в `marketplace-infrastructure`. Budget развёрнут из management account `227373271916`. `marketplace-infrastructure` не является допустимым постоянным владельцем WCC.

WCC deployment сейчас реализован через `tool/local_kubernetes_manage.py`, kind, local overlays, локальные persistent paths и HTTP на `http://localhost`. Product AWS credentials передаются как одночасовые STS environment values в Kubernetes `Secret`, после expiry требуется новый apply.

`browser-runtime` устанавливает Google Chrome и использует Playwright channel `chrome`. Existing local registry и accepted images могут быть `linux/amd64`, поэтому их raw перенос на ARM EC2 недопустим.

## Требуемый Результат

Один EC2 instance `m7g.xlarge` запускает единственную development k3s environment WCC. Пользователь управляет ей через проектный Python CLI и Session Manager, открывает UI через tunnel на `localhost:8080`, а ноутбук больше не держит локальный Kubernetes cluster.

Стабильный AWS data plane сохраняет существующие physical resources и logical IDs. Compute, network, retained volume, snapshots и lifecycle живут в отдельном stack. Product images и user WorkflowSource images собираются для exact platform runtime node, а release identity связывает чистые опубликованные исходники, target platform, image digests и Kubernetes render.

Остановка не зависит от CPU/load: среда выключается после 30 минут доказанного отсутствия SSM sessions и полезной WCC работы. Renewable external stop lease защищает от отказа host controller без hard max uptime.

ZITADEL user/grants и GlitchTip state переживают cutover и все recovery scenarios. Pre-production Product DB, workflow registry/run state и development Data/Secret/Result/Athena state пересоздаются чисто без migration framework.

## Scope И Non-Goals

В scope входят:

- новый infrastructure repository и его CloudFormation/Python/test/operations implementation;
- перенос data-plane template без замены live resources;
- новый compute stack и полный EC2/k3s lifecycle;
- Session Manager console, HTTP tunnel и SSH-over-SSM;
- retained EBS, snapshots, stop/start, replacement и restore;
- clean-source transfer, native image builds, immutable release и rollback;
- renewable AWS credential process для platform workloads;
- перенос WCC Product deployment с kind на remote k3s и удаление local branch;
- configurable UI browser-test base URL;
- bundled Playwright Chromium;
- selective state preservation/reset и полная remote acceptance;
- удаление старого WCC Budget;
- удаление WCC ownership из `marketplace-infrastructure`.

В scope не входят:

- production environment или mutation future production account;
- EKS, multi-node Kubernetes, high availability или public Product endpoint;
- NAT Gateway, paid interface VPC endpoints, Elastic IP или inbound SSH/HTTP;
- Kubernetes billing, per-user compute allocation или chargeback;
- multi-architecture OCI publication;
- Product database migrations, backward compatibility или dual local/remote deployment;
- raw transfer old local registry, Product DB or development Data;
- automatic deletion laptop state after acceptance;
- modification VPN/runtime contracts unless real compatibility verification finds a defect.

## Владение Репозиториями

### `workflow-infrastructure`

Репозиторий становится единственным владельцем:

- stable data-plane template `cloudformation/workflow-control-center-development.yaml`;
- compute template `cloudformation/workflow-control-center-development-compute.yaml`;
- local operator CLI `tool/development_environment_manage.py` и owner-local `tool/lib/**`;
- tests templates, source delivery, lifecycle, cost checkpoint, stop lease и recovery;
- maintained infrastructure operations documentation.

Один repo в будущем владеет и development, и production environments, но через разные accounts/stacks/configuration. Отдельный `workflow-infrastructure-devel` не создаётся.

### `workflow-control-center`

WCC остаётся единственным владельцем:

- Kubernetes manifests, namespaces, Kustomize и Helm integration;
- Product image build graph;
- Product database/ZITADEL/GlitchTip bootstrap;
- source-map publication and validation;
- workflow registry, rootless builder и controllers/workers;
- Product smoke, state smoke, API/auth/UI/browser acceptance;
- Data/Athena and Workflow runtime behavior.

Local kind code, overlays, commands, `.local/kubernetes` contract и `docs/local_kubernetes.md` удаляются после remote acceptance. Новый `docs/development_kubernetes.md` и `Project Verification Rules` становятся фактической operational boundary.

### `browser-runtime`

Runtime удаляет installation/selection Google Chrome и channel `chrome`, устанавливает bundled Playwright Chromium и подтверждает запуск на target platform. Proxy/profile semantics не меняются.

### `marketplace-infrastructure`

После успешного сохранения нового owner:

- оба WCC CloudFormation templates удаляются из repository;
- WCC account design удаляется без forwarding file;
- AGENTS/README и любые catalogs перестают упоминать WCC;
- production marketplace infrastructure не меняется.

Budget template не переносится: live Budget удаляется, а source template удаляется.

### `vpn-runtime` И `workflow-container-runtime`

Оба repository являются verification consumers. Их images собираются для target platform и проходят actual container/runtime acceptance. Design или code меняются только при доказанной несовместимости с утверждённым contract.

## Stable Data Plane

Template data-plane переносится source-for-source в новый owner и затем развивается только там. Stack name, account, region, parameters, outputs, logical IDs и physical resources сохраняются. Change set переноса не должен заменять или удалять S3 buckets, KMS key, IAM roles, Glue database/tables, Lake Formation registration/grants, Athena workgroup/alarms, API Gateway или Observability bucket.

Platform role получает EC2 trust и instance profile, сохраняя универсальную `AdministratorAccess` permission policy. Это не вводит name-prefix restrictions. Tenant-tagged Data/Athena roles и Lake Formation isolation сохраняют существующий least-privilege contract.

Перед и после переноса выполняются template validation, exact change-set inspection, resource identity comparison, real two-tenant Data/Athena isolation и full drift detection.

## Compute Stack

Stack `workflow-control-center-development-compute` создаёт:

- dedicated VPC;
- одну public subnet;
- Internet Gateway и public route;
- security group без inbound rules;
- optional free S3 gateway endpoint;
- EC2 instance profile для existing platform role;
- один EC2 `m7g.xlarge`;
- encrypted gp3 root/scratch `100 GiB`;
- encrypted retained gp3 `80 GiB`;
- семь daily incremental snapshots retained volume;
- Session Manager and EventBridge Scheduler lifecycle roles/resources.

Ubuntu Server 24.04 LTS AMI разрешается через Canonical public SSM parameter по `ComputeArchitecture`, а не через hardcoded AMI ID. Baseline parameters:

- `/aws/service/canonical/ubuntu/server/24.04/stable/current/arm64/hvm/ebs-gp3/ami-id`;
- `/aws/service/canonical/ubuntu/server/24.04/stable/current/amd64/hvm/ebs-gp3/ami-id`.

Primary scaling direction:

- single-core/I/O: `m7g.xlarge -> m8g.xlarge -> m9g.xlarge`;
- parallel build CPU: `c6g.2xlarge -> c7g.2xlarge -> c8g.2xlarge -> c9g.2xlarge`;
- memory: `r7g.xlarge -> r8g.xlarge ->` current newer `r*.xlarge`.

No Elastic IP exists. Instance may receive ephemeral public address for outbound Internet, while every operator connection targets instance ID through SSM.

## k3s И Security

Bootstrap installs a pinned k3s release, enables secrets encryption, disables Traefik and does not expose Kubernetes API externally. WCC deploys tracked ingress-nginx and Product manifests.

IMDSv2 is required with hop limit `1`. Pods cannot route to `169.254.169.254`. Service accounts, network policies, capabilities, devices, requests/limits, probes, termination and cleanup remain explicit.

Root/scratch volume owns OS, k3s datastore, containerd, Docker/BuildKit cache, images and temporary data. Retained volume owns:

```text
/srv/workflow-control-center/
  glitchtip/
  observability/
  postgres/
  release/
  secrets/
  workflow-registry/
  workflow-run/
```

Retained volume uses `DeletionPolicy: Retain` and `UpdateReplacePolicy: Retain`. Root volume deletes on replacement and has no snapshot schedule. Restore storage is bounded to current retained volume plus one previous rollback volume; a later explicit restore removes only an older exact-owned detached rollback before creating its replacement.

Cloud-init/UserData owns only mount/format, minimal packages without `apt upgrade`, pinned k3s, Docker/Buildx and SSM/systemd substrate. Product source, infrastructure control source, Product secrets, GitHub credentials and deployment logic are forbidden there. Create/replacement orchestration publishes exact clean infrastructure source and installs the lifecycle controller only after successful cloud-init, then proves controller readiness before disabling the replacement guard.

Host tooling uses reproducible Python 3.14 venv. Standalone shell scripts are not introduced.

## Session Manager

No inbound `22`, `80` or `443` rules exist.

- Product HTTP uses one SSM port-forwarding tunnel to local `8080`.
- Console uses Session Manager.
- SSH, SCP, rsync and Remote SSH IDE use SSH-over-SSM.
- Active sessions are observed through Systems Manager session state for the exact instance ID.

Product origins through tunnel:

- `http://localhost:8080`;
- `http://zitadel.localhost:8080`;
- `http://errors.localhost:8080`.

The workstation prerequisites are AWS CLI v2 and Session Manager plugin. Public address and DNS do not enter Product config.

## Renewable Stop Lease

Early stop requires 30 uninterrupted minutes with no active SSM sessions and WCC probe `idle`. WCC reports `busy` while any WorkflowRun, build, VPN validation, Data/Athena operation, recovery or required cleanup remains unfinished. Probe or AWS failure is `busy`.

Before shutdown controller repeats both checks, cordons node, gracefully drains, stops Product containers and k3s, then powers off. Failure uncordons and leaves instance running. CPU, load and network counters never decide idleness.

Before every start, the external operator creates one-time EventBridge Scheduler `StopInstances` at `now + 2 hours`. Failure to create it blocks start. Host renews the schedule every 30 minutes while it proves active SSM or useful Product work. Healthy long work can continue indefinitely; there is no hard deadline.

If host/controller stops renewing, AWS stops instance within two hours of last successful renewal. Schedule uses `ActionAfterCompletion=DELETE`, and ordinary stop removes pending schedule.

Tests use controlled clocks/provider fakes for normal timing and one real short acceptance configuration when AWS proves the same transitions without waiting production-length intervals.

## AWS Credentials

Existing platform role becomes the EC2 instance profile role. AWS rotates its temporary credentials.

Host refresher uses `aws configure export-credentials --format process`, writes standard `credential_process` JSON atomically to a narrowly distributed Kubernetes `Secret`, and refreshes before expiry. Backend/platform workers mount that file read-only and use standard AWS SDK refresh.

AWS access key, secret and token are not delivered through environment variables. Workflow, browser and VPN Pods never mount the credential file. Credentials never enter retained secret export, release manifest, logs, image layers or GlitchTip.

Laptop SSO is needed only to manage CloudFormation and SSM. Its expiry cannot break the running Product. Refresh failure becomes fail-closed readiness/error after current EC2 session expires; static and local fallbacks do not exist.

## Source Delivery И Release

`development_environment_manage.py deploy` inventories required repositories and accepts only clean exact source commits published upstream. It records repository URL, SHA and content digest, transfers only tracked required files through rsync over SSH-over-SSM, verifies manifest on host and atomically publishes source release from staging.

EC2 has no GitHub credentials and does not clone source. Untracked, modified or unpublished source blocks deployment.

All platform images are built on EC2 with Docker Buildx for exact runtime node platform and published into retained cluster-local registry by immutable digest. User WorkflowSource images continue through rootless cluster BuildKit.

Deployment:

1. resolves one homogeneous Linux OCI platform from eligible nodes;
2. stages exact sources;
3. builds all images and source maps;
4. records immutable digests in exact Kustomize render;
5. invokes WCC-owned apply/readiness/smoke;
6. marks release current only after complete acceptance;
7. reapplies previous exact release manifest after rollout failure.

Release manifest contains source URLs/SHAs/content digests, target platform, image digests, Kubernetes render digest and deployment timestamp. It is runtime provenance, not a commit pin in `requirements*.txt`.

## Runtime Platform И Browser

All WorkflowRun-eligible nodes must report the same `(operatingSystem, architecture)`. OS must be Linux. Mixed nodes or unknown platform fail before build.

Normalized OCI platform is passed explicitly to every Docker and BuildKit invocation. BuildKit automatic `BUILDPLATFORM`, `TARGETPLATFORM`, `TARGETOS` and `TARGETARCH` values may be consumed. Accepted OCI manifest/config must equal target. Host builder architecture is not inferred as Product target.

Target platform is immutable in each build attempt, WorkflowSourceVersion candidate and Product release. Architecture change never rewrites an existing image. Pre-production architecture change performs clean Product reset and creates new version/build identities. Multiarch remains a separate future design.

`browser-runtime` uses bundled Playwright Chromium and removes Google Chrome/channel `chrome`. Browser remains an ordinary consumer of platform proxy map.

## Cost Boundary

The approved architecture in `workflow-infrastructure/design/development-environment.md` is the cost checkpoint. Each proposal calculates the net recurring monthly delta from that checkpoint using the same current prices and usage assumptions:

- cumulative delta `<= USD 10/month` needs no separate approval;
- cumulative delta `> USD 10/month` needs approval before apply;
- reductions offset increases;
- approved proposal becomes the next checkpoint;
- unbounded cost requires approval.

Projected one-time spend above `USD 10` requires separate approval. Security/account/production boundaries remain independent. No AWS Budget replaces this policy.

Before compute apply, current regional prices and assumed active hours, gp3, snapshots, S3, KMS, Glue, Athena, API Gateway and data transfer are recorded. Small implementation details within approved architecture do not restart approval unless the cumulative threshold is crossed.

## Selective State Cutover

Before mutation, local state-smoke proves current ZITADEL user, password login/OIDC, Product project and grants, GlitchTip project/users/DSN, uploaded files and current symbolication.

Preserved through logical export/import:

- ZITADEL database and long-lived runtime secrets;
- exact user `antonov.andrey@gmail.com`, password hash, provider links and Product grants;
- GlitchTip database, OIDC users/configuration, uploaded files and retained source maps.

Recreated clean:

- Product databases `apwid` and `apwid_test`;
- workflow registry and WorkflowRun storage;
- development Data, Secret and Result objects including all versions/delete markers;
- dynamic Glue/Athena catalog and Product projections;
- all Product, browser, VPN and WorkflowSource images for target platform.

Identity and observability exports are secret operational artifacts and are never committed or logged. Product migrations, compatibility schema, dual write/read or local/remote synchronization are absent.

После очистки required development `VpnConfig name="tr"` создаётся заново через обычный Product Secret upload/accept flow из operator-owned `config.json` и `tr.ovpn`, после чего создаётся и проверяется новая `VpnConfigVersion`. Raw S3 copy, перенос старых VersionIds или восстановление purged Secret objects запрещены.

Old laptop kind cluster, data and registry remain untouched until full remote acceptance. Their deletion is outside this goal unless the user explicitly requests it later.

## Budget Removal

The obsolete Budget stack in management account `227373271916` and its source template are removed. This exact deletion is already explicitly approved.

Implementation must verify management-account caller identity, exact stack name, linked-account filter and absence of unrelated budgets before deletion. It must delete only the WCC development Budget and report the mutation. This one operation does not grant general management-account authority.

## Verification

### Deterministic Source Verification

Each repository runs its required tests and artifact checks. Infrastructure templates pass YAML/CloudFormation validation, `cfn-lint`, exact AWS `validate-template`, change-set review and `git diff --check`. Python orchestration passes targeted pytest, compile/import and CLI help/smoke.

Tests prove target-platform normalization, mixed-node rejection, source cleanliness/upstream checks, content manifest verification, immutable release, rollback, credential redaction, stop-lease state machine, cost-delta calculation and retained-resource protection.

### AWS Data Plane

Before and after owner transfer:

- stack is `UPDATE_COMPLETE`;
- every stable physical resource and logical ID matches;
- full drift is `IN_SYNC`;
- buckets retain public blocks, versioning, KMS, lifecycle and exact policies;
- Lake Formation location remains `VERIFIED`;
- real two-tenant suite proves positive/negative `GetTable`, `GetTables`, `SearchTables`, `SHOW TABLES` and `SELECT`;
- Data/Secret/Result/Observability read/write/cleanup contracts pass.

### Compute И Access

Acceptance proves no inbound rules, no EIP/NAT, IMDSv2, SSM readiness, console, port forwarding, SSH-over-SSM, retained mount, k3s encryption/configuration, Pod IMDS denial, resource limits and target platform.

Idle lifecycle proves:

- no stop during active SSM;
- no stop during every WCC busy category;
- stop after 30 minutes proven idle;
- renewal beyond two hours for healthy long work;
- stop after renewal loss;
- schedule deletion after completion/ordinary stop;
- start failure when initial schedule is unavailable.

### Product И UI

WCC executable verification follows its `AGENTS.md`. UI changes run tests/build, publish exact clean candidate, remote deploy, keep SSM tunnel, run browser suite with `WCC_UI_BASE_URL=http://localhost:8080`, then complete manual current-assets workflow.

Acceptance proves Product API/auth/UI, ZITADEL login and grants, GlitchTip ingestion/symbolication/redaction, source-map private archive, Data/Athena, rootless WorkflowSource build, bundled Chromium browser runtime, VPN runtime and one real WorkflowRun through UI.

One real WorkflowSource build must produce target-platform candidate and pass platform/publisher tests. One WorkflowRun must exercise Data checkpoints, finalization, browser/VPN when configured and replacement from last accepted savepoint without parallel writer.

### Recovery

Full state and Product acceptance repeats after:

1. stop/start same instance and volume;
2. replacement instance with same retained volume;
3. new retained volume restored from chosen snapshot.

Each scenario proves exact ZITADEL user/grants, GlitchTip state, registry, Product state according to lifecycle, WorkflowRun recovery, AWS data plane, UI and release identity.

### Semantic Acceptance

After implementation appears complete, independently reread all applicable owner contracts and actual source/runtime state. Fix every uncovered requirement or contradiction, rerun invalidated mechanical and live checks, then repeat the complete semantic audit from owner discovery. Completion requires one fresh full pass with no new finding and no uncovered requirement.

The final state must have one infrastructure owner, one remote deployment path, no WCC artifacts in `marketplace-infrastructure`, no local-kind branch, no Google Chrome/channel `chrome`, no hardcoded target architecture, no static/platform credentials in untrusted Pods, no fixed Budget, no hard uptime deadline and no loss of preserved ZITADEL/GlitchTip state.
