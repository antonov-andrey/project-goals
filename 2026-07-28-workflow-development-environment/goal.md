# Удалённая Среда Разработки `Workflow Control Center`

## Outcome

Полностью реализовать конечное состояние из `.spec/2026-07-28-workflow-development-environment-spec.md`: перенести единственное владение инфраструктурой WCC в `workflow-infrastructure`, сохранить существующий AWS data plane, создать отдельный EC2/k3s compute stack, перевести Product deployment с laptop kind на exact remote release через Session Manager, собирать все images для фактической runtime platform, перейти на bundled Playwright Chromium и завершить selective state cutover с сохранением ZITADEL/GlitchTip и полной recovery acceptance.

После завершения `marketplace-infrastructure` не содержит WCC artifacts, local-kind deployment branch отсутствует, development environment управляется одним воспроизводимым Python CLI, AWS credentials автоматически обновляются без laptop SSO и Pod restart, а idle shutdown использует WCC activity proof и renewable external stop lease без hard uptime deadline.

## Source Contracts

- `.spec/2026-07-28-workflow-development-environment-spec.md`: координирующий scope, исходное состояние, destructive cutover, cross-repository изменения и полная acceptance.
- `DESIGN.md`: корневая граница и routing инфраструктурного владельца.
- `design/development-environment.md`: account, stacks, compute, network, storage, k3s, credentials, source delivery, target platform, lifecycle, cost, cutover и recovery.
- `docs/development-environment-operations.md`: canonical operator interface реализованной среды.
- `AGENTS.md`: repository structure, AWS authority, change workflow, secrets и verification `workflow-infrastructure`.
- `workflow-control-center` — `DESIGN.md`: Product owner routing.
- `workflow-control-center` — `design/data-storage.md`: development AWS adapter и Data/Athena boundary.
- `workflow-control-center` — `design/workflow-runtime.md`: runtime image platform, build, execution и browser capability.
- `workflow-control-center` — `design/zitadel.md`: preserved identity state и operational acceptance.
- `workflow-control-center` — `design/observability.md`: GlitchTip, releases, source maps и current-assets telemetry acceptance.
- `workflow-control-center` — `docs/development_kubernetes.md`: Product deployment, persistent state, diagnostics и remote acceptance.
- `workflow-control-center` — `AGENTS.md`: exact UI handoff order и Product verification.
- `browser-runtime` — `DESIGN.md`: bundled Chromium, target platform, profiles, proxy и security boundary.
- `browser-runtime` — `README.md`: public runtime and development interface.
- `marketplace-infrastructure` — `AGENTS.md` и `README.md`: marketplace-only repository boundary.

## Constraints

Изменения `workflow-infrastructure`, `workflow-control-center`, `browser-runtime` и `marketplace-infrastructure` образуют одну цель. `vpn-runtime` и `workflow-container-runtime` участвуют в real target-platform verification и меняются только при доказанной несовместимости. Existing unrelated user changes сохраняются.

Account `463564115167` и profile `workflow-control-center-devel` имеют standing development authority без approval pause; все material mutations сообщаются при handoff. Production и другие accounts не изменяются. Единственное разрешённое management-account действие — уже утверждённое удаление exact obsolete WCC Budget после проверки identity и target.

Существующие physical data-plane resources, logical IDs и tenant-isolation semantics сохраняются. Product DB, workflow registry/run state и development Data/Secret/Result/catalog пересоздаются без migrations, compatibility bridge или dual deployment. ZITADEL/GlitchTip сохраняются. Laptop state не удаляется.

Один homogeneous Linux target определяется по WorkflowRun-eligible nodes и передаётся build явно. Hardcoded `linux/arm64`, builder-default target, silent image rewrite и Google Chrome/channel `chrome` запрещены.

Source deployment принимает только clean exact upstream commits и не хранит GitHub credentials на EC2. Static AWS keys, AWS credential environment variables platform Pods и любые platform credentials workflow/browser/VPN Pods запрещены.

Текущая утверждённая архитектура является cost checkpoint. Изменение свыше cumulative `USD 10/month`, unbounded cost или one-time spend свыше `USD 10` требует отдельного согласования до apply; уже утверждённая baseline architecture не требует повторного согласования.

Файлы `.spec/2026-07-28-workflow-development-environment-spec.md` и `.spec/2026-07-28-workflow-development-environment-goal.md` остаются ignored, untracked и сохранёнными. Они не удаляются при завершении цели.

## Verification

Выполнить все обязательства раздела `Verification` спецификации и каждого applicable `AGENTS.md`: deterministic tests, complete mechanical checks, CloudFormation validation/change sets/drift, real AWS data-plane isolation, SSM/compute/lifecycle, Product UI/current-assets, native browser/VPN/workflow images, one real WorkflowRun и три complete recovery scenarios.

После предполагаемого завершения провести полный независимый semantic audit от owner discovery, исправить все findings, повторить invalidated checks и снова провести полный audit. Продолжать цикл audit/fix до одного свежего прохода без новых findings и uncovered requirements.

Завершение включает логические commits, push всех затронутых repositories и доказательство clean worktrees с exact local HEAD равным upstream branch. Успешный checker, stack status, node readiness или частичный smoke не заменяет требуемую семантическую и live acceptance.
