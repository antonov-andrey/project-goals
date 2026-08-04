# Shared Persistence, Kubernetes, ZITADEL, And Product API Standards

## Outcome

Make `project-standards` the single reusable owner of the approved table-lifecycle profiles, Kubernetes operational rules, ZITADEL integration rules, Product REST API backend mechanics, and frontend capability-consumption boundary. Migrate the protected `workflow-control-center` and `marketplace-tr-priority` instruction/design sources without semantic loss, add precise automatic Kubernetes and ZITADEL classification, publish and install the updated provider, and leave every affected consumer with only real project-specific overlays.

## Source Contracts

- `.spec/2026-07-25-persistence-kubernetes-zitadel-rest-standards-spec.md`: complete approved target, protected source-to-target ledger, classifier contract, repository scope, and verification design.
- `AGENTS.md`: coordinating provider ownership, task-artifact, validation, and publication contract.
- `plugins/project-standards/skills/project-instruction-developer/references/instruction-protocol.md`: external-standard selection, protected instruction migration, and duplicate-owner rules.
- `plugins/project-standards/skills/project-standard-audit/references/project-standard-audit.md`: workspace semantic acceptance and protected-ledger audit owner.
- `plugins/project-standards/skills/project-standardize/references/project-standardization.md`: workspace discovery, classification, mutation, and verification owner.
- `plugins/project-standards/skills/sqlalchemy-developer/references/sqlalchemy.md`: SQLAlchemy and table-lifecycle owner.
- `plugins/project-standards/skills/kubernetes-developer/references/kubernetes.md`: Kubernetes owner.
- `plugins/project-standards/skills/rest-api-server-developer/references/rest-api-server.md`: Product REST API backend owner.
- `plugins/project-standards/skills/react-ui-developer/references/react-ui.md`: Product capability-consumer and account-scoped UI owner.
- Repository `workflow-control-center`, `AGENTS.md`, `DESIGN.md`, `design/backend.md`, `design/frontend.md`, `design/zitadel.md`, `design/observability.md`, and `docs/local_kubernetes.md`: protected consumer instructions and stable WCC product/operational specializations.
- Repository `marketplace-tr-priority`, `AGENTS.md`: protected refreshable-snapshot consumer selection and repository verification contract.

## Constraints

- Follow the paired specification ledger exhaustively; do not summarize away or silently drop a source clause.
- Keep runtime code in WCC until a second real runtime consumer requires one shared implementation owner; do not copy or prematurely extract `ProductApiRouter`.
- Do not change schemas, persisted data, routes, API payloads, capability keys, role assignments, runtime behavior, Kubernetes state, ZITADEL state, or Product UI behavior.
- Classify only real technology boundaries; identifier, proxy-path, prose, and fixture mentions of ZITADEL remain negative evidence.
- Preserve every `.spec` pair, keep it ignored and untracked, and create no compatibility owner or completion-evidence artifact.
- Commit logically and push every changed tracked repository; finish only with clean worktrees and `HEAD == origin/main`.

## Verification

Satisfy the full verification design in the paired specification and every applicable provider and consumer handoff contract. Validate and test the provider, classifier, new skill, installed cache-busted plugin, protected semantic migrations, existing WCC route/capability and ZITADEL behavior boundaries, workspace-wide standard selection, and remote publication.

After presumed completion, repeatedly perform the specification's `Terminal Completion Audit` over the whole current scope from scratch, fix every finding, rerun affected verification, and start another full audit until one post-fix audit finds no incomplete requirement.
