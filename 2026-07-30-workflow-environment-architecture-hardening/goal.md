# Укрепление Архитектуры Сред `Workflow Control Center`

## Outcome

Полностью реализовать конечное состояние из `.spec/2026-07-30-workflow-environment-architecture-hardening-spec.md`: разрешать latest `workflow-container-contract` ровно один раз на новый Product release и дальше использовать его exact immutable identity, выполнить destructive pre-production cutover без legacy technical implementations, concrete obsolete migrations или compatibility branches, сохранить generic versioning/migration/rollback mechanisms текущей системы, отделить common Product/Kubernetes/data-plane contracts от development и future production adapters, устранить подтверждённые AWS/Kubernetes/supply-chain/UI weaknesses и завершить полную remote development acceptance без создания production либо второй development environment.

После завершения WCC runtime requirements и image builds не содержат второго moving contract source; recovery принимает только единственные текущие source/Product manifest contracts, а release manifest связывает selector/ref/commit/package/content provenance с каждым consumer image. Existing pre-cutover Product/workflow state и compatibility code удалены, ZITADEL/GlitchTip state сохранён, development `primary` остаётся одним EC2/k3s environment, future production однозначно определена как EKS architecture, а common Product implementation не дублируется.

## Source Contracts

- `.spec/2026-07-30-workflow-environment-architecture-hardening-spec.md`: полный implementation scope, transition, security boundaries и terminal acceptance.
- `DESIGN.md`: корневой infrastructure routing.
- `design/environment-model.md`: common implementation, environment identity, release-source resolution, Kubernetes и AWS data-plane invariants.
- `design/development-environment.md`: exact development EC2/k3s specialization, standing account authority, lifecycle, recovery и cost.
- `design/production-environment.md`: mandatory future EKS specialization without current production mutation.
- `AGENTS.md`: repository ownership, AWS authority, change workflow и verification `workflow-infrastructure`.
- `workflow-control-center/DESIGN.md`: Product owner routing.
- `workflow-control-center/design/workflow-runtime.md`: exact dependency consumption, disposable builder, trusted registry push и runtime contract.
- `workflow-control-center/design/data-storage.md`: reference-aware retention, tenant IAM и common data-plane semantics.
- `workflow-control-center/design/frontend.md`: UI security headers, CSP and exact UI acceptance.
- `workflow-control-center/AGENTS.md`: current remote Product/UI handoff order.
- `workflow-control-center/docs/development_kubernetes.md`: implemented remote deployment, retained state, diagnostics and recovery operations.
- `workflow-container-runtime/docker/platform-base/Dockerfile` и `browser-runtime/docker/playwright/Dockerfile`: existing named-context consumers, изменяемые только при доказанном contract defect.

## Constraints

Изменения `workflow-infrastructure`, `workflow-control-center`, `workflow-container-contract`, `workflow-container-runtime`, `brand-size-chart` и workflow-container skills в `agent-plugins` образуют одну цель. `browser-runtime` и `vpn-runtime` являются verification consumers и меняются только при доказанном incompatibility/defect. Existing unrelated user changes сохраняются.

Account `463564115167` и profile `workflow-control-center-devel` имеют standing development authority без approval pause; все material mutations сообщаются при handoff. Account `227373271916`, future production account и любые другие accounts не изменяются.

Новый Product release разрешает remote default-branch `HEAD` contract один раз. Hardcoded commit pins в consumers, VCS dependency в WCC runtime requirements, repeated fetch, build-time Git resolution, implicit fallback и automatic override запрещены. Recovery принимает только current technical manifest shape и не требует network; обычная release history и rollback, созданные текущей implementation, сохраняются.

Перед первым новым deploy selective Product reset удаляет старые Product databases, все Product S3 object versions/delete markers/multipart uploads, dynamic Glue tables, workflow volumes, registry contents, retained Product releases и Product-tool environments, сохраняя на месте и проверяя ZITADEL и GlitchTip. Compatibility readers, pre-hardening transitions, legacy CLI/runtime branches, source sanitation, one-time identity dump-import, obsolete workflow migration declarations/scripts и historical concrete-workflow fallbacks запрещены. Generic DB/workflow-input migration tooling, `WorkflowSourceVersion` и будущие immutable versions текущего interface сохраняются как current system mechanisms.

Один current EC2/k3s environment `primary` сохраняет exact physical state identities. Parameterization не создаёт вторую environment. Production resources и sandbox runtime не реализуются в этой цели.

CloudFormation change set не может неявно удалять или заменять state-bearing S3, KMS, Glue, Lake Formation, Athena, retained EBS либо preserved identity/observability state. Exact destructive pre-production Product reset из paired specification явно разрешён. Concrete migrations старого cutover state, dual deployment и duplicate Product implementation запрещены; generic database/workflow-input migration frameworks сохраняются.

Untrusted BuildKit не получает Git/registry/AWS/Kubernetes credentials или writer-registry path. Optional platform base читается только через exact read-only repository proxy; publication выполняет trusted push boundary.

Текущая утверждённая архитектура остаётся cost checkpoint. Вторая environment не создаётся; cumulative recurring delta свыше `USD 10/month`, unbounded cost или one-time spend свыше `USD 10` требует отдельного согласования до apply.

Файлы `.spec/2026-07-28-workflow-development-environment-{spec,goal}.md` и `.spec/2026-07-30-workflow-environment-architecture-hardening-{spec,goal}.md` остаются ignored, untracked и сохранёнными. Они не удаляются автоматически.

## Verification

Выполнить все обязательства раздела `Verification` paired specification и каждого applicable `AGENTS.md`: deterministic tests, complete mechanical checks, semantic owner review, destructive-reset inventory/preservation proof, CloudFormation validation/change sets/drift, real AWS tenant isolation, credential refresh without restart, disposable concurrent builders, registry read/write isolation, strict current-implementation exact-source release/rollback/recovery, preservation of generic migration/versioning mechanisms, Product API/auth/UI/browser/VPN/workflow acceptance.

Любое изменение `ui/**` или user-visible UI behavior проходит exact current WCC sequence: UI tests/build, clean candidate commit/push, remote deploy, active SSM tunnel, browser suite and complete manual verification against current assets.

После предполагаемого завершения провести полный независимый semantic audit от owner discovery, исправить все findings, повторить invalidated checks и снова провести полный audit. Продолжать цикл audit/fix до одного свежего полного прохода без новых findings и uncovered requirements. Дополнительные evidence-documents не создавать.

Завершение включает логические commits, push всех затронутых repositories и доказательство clean worktrees с exact local HEAD равным upstream branch. Успешный checker, source diff, stack status, Pod readiness или частичный smoke не заменяет требуемую semantic and live acceptance.
