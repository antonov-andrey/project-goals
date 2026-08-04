# Shared Persistence, Kubernetes, ZITADEL, And Product API Standards

## Required Outcome

`project-standards` becomes the single reusable owner of the approved table-lifecycle profiles, Kubernetes operational rules, ZITADEL integration rules, and Product REST API backend rules currently duplicated or retained in consumer projects. A project that actually contains one of these technologies or boundaries selects the corresponding capability through `Required Standards`; `project-standardize` detects the capability from real project artifacts and fails when the selection is missing.

`workflow-control-center` and `marketplace-tr-priority` retain only their concrete paths, product behavior, domain policy, operational values, and explicit profile selection. They no longer duplicate reusable provider prose. Existing runtime behavior, database schemas, persisted data, routes, and UI behavior do not change as part of this ownership migration.

## Coordinating Repository And Document Mode

The coordinating repository is `project-standards` because it owns every shared target capability and the classifier that selects those capabilities. This pair uses a dedicated implementation specification because the change spans one provider, two protected consumer instruction files, several stable design owners, one new capability skill, classifier behavior, plugin publication, and workspace-wide acceptance.

The paired task artifacts are:

- `.spec/2026-07-25-persistence-kubernetes-zitadel-rest-standards-spec.md`;
- `.spec/2026-07-25-persistence-kubernetes-zitadel-rest-standards-goal.md`.

They remain ignored and untracked under the existing exact `/.spec/` rule and are not deleted at any task state without an explicit user request.

## Verified Current State

- The current `project-standardize --workspace-root ~/Projects --check` result is valid for all currently discovered repositories before this change.
- `project-standards:sqlalchemy-developer` currently requires each consumer to own table lifecycle locally. Consequently:
  - repository `workflow-control-center`, `AGENTS.md` section `Table Lifecycle Rules` owns mutable product-state and append-only log semantics;
  - repository `marketplace-tr-priority`, `AGENTS.md` section `Table Lifecycle Rules` owns refreshable-snapshot semantics.
- `project-standards:kubernetes-developer` already owns the common local-state classification, but repository `workflow-control-center` still contains:
  - generic one-off migration `Job` rules in `AGENTS.md` section `Database Schema Migration Specializations`;
  - stale references to a nonexistent local `Local Kubernetes State Contract` in `docs/local_kubernetes.md` and `design/observability.md`.
- `project-standards:rest-api-server-developer` currently leaves router, role, delegated-user, and lifecycle semantics in consumer overlays. Repository `workflow-control-center`, `AGENTS.md` section `Product API Rules` therefore owns reusable `ProductApiRouter`, `/for-user/{zitadel_user_id}`, capability, OpenAPI, session, audit, secret-response, and route-test contracts.
- Repository `workflow-control-center` is the only current repository with an actual ZITADEL integration. Its tracked artifacts include the ZITADEL deployment, backend identity client, OIDC UI client, tests, and `design/zitadel.md`.
- A field, path fragment, or runtime value containing `zitadel_user_id` is not by itself a ZITADEL integration. In particular, the browser/VPN proxy identity form `{zitadel_user_id}/{vpn_config.name}` must not cause repositories such as `browser-runtime` to select `zitadel-developer`.
- `workflow-control-center/backend/api_router.py` is the sole current runtime implementation of `ProductApiRouter` and its registry. `backend/api/capability.py` derives `GET /capability` from the same route metadata. No second current consumer justifies runtime extraction.
- `project-standards:react-ui-developer` already requires capability-driven Product actions, but it does not explicitly forbid a parallel frontend action map. It also incorrectly scopes personal presentation preferences to the effective delegated account, while the approved `workflow-control-center` contract scopes them to the authenticated account and keeps delegation from changing them.

## Scope

The change includes:

- reusable lifecycle profiles in `sqlalchemy-developer`;
- expanded reusable Kubernetes state and one-off `Job` contracts;
- one new `zitadel-developer` capability skill;
- the reusable Product REST API backend contract in `rest-api-server-developer`;
- the capability-consumer and authenticated-account preference rules in `react-ui-developer`;
- classifier signals and tests for automatic Kubernetes and ZITADEL selection;
- protected source-to-target migrations in `workflow-control-center` and `marketplace-tr-priority`;
- provider validation, local plugin update, consumer semantic acceptance, commits, and pushes for every changed tracked repository.

## Non-Goals

- Do not move `workflow-control-center` runtime code into `project-standards`.
- Do not create a shared Python runtime package, submodule, compatibility wrapper, or copied `ProductApiRouter` implementation.
- Do not change REST paths, request or response shapes, capability keys, role assignments, database schemas, table data, timestamp values, Kubernetes workloads, ZITADEL state, or Product UI behavior.
- Do not retrofit lifecycle columns onto existing tables.
- Do not generalize WCC domain behavior for Workflow, VPN, Data, Athena, observability, or storage into the provider.
- Do not select `zitadel-developer` merely because documentation, fixtures, identifiers, proxy names, or persisted foreign identity fields mention ZITADEL.
- Do not change another repository only for symmetry.
- Do not delete any `.spec` file.

## Approved Ownership Model

### SQLAlchemy Table Lifecycle Profiles

`project-standards:sqlalchemy-developer`, `references/sqlalchemy.md` owns three named reusable profiles and the semantic selection boundary:

- `mutable product-state lifecycle` applies to mutable product state whose current row is authoritative:
  - fields are `t_create`, `t_update`, and `is_deleted`;
  - creation uses one exact current UTC timestamp with microsecond precision for `t_create` and `t_update`;
  - update sets `t_update` to the exact current UTC timestamp with microsecond precision;
  - soft delete sets `is_deleted=true` and sets `t_update` to the exact current UTC timestamp with microsecond precision;
  - ordinary readers exclude `is_deleted=true`;
  - runtime domain operations do not physically delete rows.
- `append-only log lifecycle` applies to log, event, audit, and history rows:
  - `t_create` is required;
  - `t_update` and `is_deleted` are absent unless one narrower stable owner defines a real exception;
  - creation uses current UTC with microsecond precision;
  - runtime domain operations do not update, soft-delete, or physically delete rows;
  - cleanup occurs only through retention, rotation, partition drop, migration, or maintenance.
- `refreshable snapshot lifecycle` applies when a table is refreshed from a source over one complete lifecycle scope:
  - fields are `t_create`, `t_update`, and `is_deleted`;
  - the profile is not a retrofit rule for existing tables;
  - `t_update` means refreshed-at and changes for every row refreshed or otherwise modified in the current scope, including unchanged payloads and rows marked stale;
  - the default complete scope is an unfiltered run, while a narrower stable owner may define one complete partition;
  - stale finalization runs only for a complete lifecycle scope and is skipped for filters narrower than that scope;
  - stale rows are soft-deleted and ordinary readers exclude `is_deleted=true`;
  - lifecycle timestamps preserve the currently approved UTC precision without microseconds for this profile.

Externally owned service tables are outside consumer product lifecycle profiles. A narrower stable domain contract may select a different lifecycle, but overlapping column names alone do not change profile semantics. Existing unclassified or legacy tables are not automatically migrated.

Every governed non-external product ORM table must select exactly one applicable shared profile unless a narrower stable owner defines another complete lifecycle.

Repository `marketplace-tr-priority` retains only its project-specific selection that stable relational business-data tables use the shared `refreshable snapshot lifecycle` by default unless a narrower owner selects another profile. Repository `workflow-control-center` does not retain a copied lifecycle section; its product models follow the shared semantic profiles and its durable operation state machines remain owned by `design/persistence.md`.

### Kubernetes

`project-standards:kubernetes-developer`, `references/kubernetes.md` remains the single owner of:

- workload controller, service account, resources, probes, termination, restart, networking, readiness, recovery, and cleanup ownership;
- the distinction between tracked deployment configuration, runtime `Secret`, persistent service data, and temporary data;
- the prohibition on tracked or ordinarily logged runtime secrets;
- retained storage for persistent state and `emptyDir` only for disposable state;
- one-off operation execution through a named `Job` using an explicitly selected runnable image;
- waiting for the real completion boundary, collecting logs, and deleting the `Job` after success;
- leaving failure observable and recoverable instead of deleting its only diagnostic evidence;
- manifest rendering and validation before mutation and live behavior verification after deployment changes.

WCC-specific namespace names, images, retained host paths, AWS account details, local `kind` commands, UI handoff ordering, workflow replacement fencing, and operational smoke commands remain in repository `workflow-control-center`.

### ZITADEL

The new `project-standards:zitadel-developer` skill owns reusable ZITADEL integration behavior:

- one shared OIDC session and token-lifecycle owner, using `react-oidc-context` when the governed UI uses React;
- one immutable ZITADEL subject/user id as authorization identity, with email and labels used only for presentation;
- server-side validation or introspection of the current bearer token and active identity;
- one shared direct ZITADEL API client rather than page-local or route-local protocol logic;
- ordinary browser identity operations calling ZITADEL through that client rather than a Product backend proxy;
- explicit separation of current authenticated `zitadel_user_id` and Product `effective_zitadel_user_id`;
- Product delegation never changing the browser principal and never implicitly entering direct identity API calls;
- role decisions derived only from the current validated identity context;
- profile links using explicit identity inputs without hidden current-user fallback;
- tokens, authorization codes, state, nonce, cookies, private keys, credentials, raw callback URLs, and callback query strings excluded from persistence, logs, telemetry, URLs, and ordinary caches;
- identity telemetry carrying immutable ids and safe normalized stages rather than email, name, login hint, or raw protocol material;
- Product observability limited to Product-owned OIDC stages and excluded from ZITADEL pages and cross-origin silent-renew content;
- ZITADEL server observability remaining owned by ZITADEL logs and metrics rather than being represented as complete by the Product browser SDK;
- ZITADEL identity persistence remaining independent from destructive Product-state reset;
- explicit account switch, profile links, renewal, logout, and identity-context cleanup;
- extension only through one new normalized identity value needed by multiple consumers, one new direct ZITADEL API operation in the shared client, or one new identity provider behind the same normalized boundary;
- verification of success, invalid/inactive identity, renewal failure, current/effective separation, secret redaction, and identity persistence boundaries.

Concrete WCC database names, Helm values, local users, Product admin grants, `state-smoke`, GlitchTip client configuration, S3 session tags, Secret Data owner policy, and Workflow grants remain WCC-owned.

Outbound transport, environment-backed configuration, and React presentation mechanics continue to reference `http-api-client-developer`, `runtime-config-developer`, and `react-ui-developer` rather than being copied into the ZITADEL skill.

### Product REST API Backend

`project-standards:rest-api-server-developer` owns the reusable Product API server contract. Its stable contract includes:

- concrete route modules, route-local request/response schemas, and route-local helpers under `backend/api/**`, while `backend/**` outside that subtree owns only application bootstrap, dependency wiring, backend configuration, router infrastructure such as `backend/api_router.py`, boundary validation such as `backend/validate.py`, and the package surface;
- every backend route registered through `ProductApiRouter` and classified as Product or infrastructure API;
- `ProductApiResource` as the declarative standard-resource mechanism for `create`, `list`, `get`, `update`, `delete`, `archive`, `block`, `publish`, delegated variants, capabilities, and OpenAPI, and `ProductApiRouter` as the unique-command and route-registry mechanism;
- declared exact-equality list filters applied over owner scope before lifecycle filters, pagination, and status counts;
- lifecycle and declared parent relations checked before domain mutation, while read remains available only where the declared lifecycle permits it;
- explicit public access through `ProductApiAccess.from_public()` and role-restricted access through `ProductApiAccess.from_role(...)`, with multiple roles forming an OR allowlist and every access declaration evaluated before domain logic;
- authenticated active ZITADEL bearer-token access by default when `zitadel-developer` applies;
- Product role checks using the current validated identity context and never a duplicate local `user` role;
- OpenAPI security matching access metadata, complete descriptions, deterministic URL path order, exact method order `GET`, `POST`, `PATCH`, `DELETE`, and canonical persisted/domain field identity;
- request and response schemas exposing only intended Product fields, excluding service-only fields such as `is_deleted`, avoiding field-renaming mapper layers, and preserving canonical persisted values except for required JSON, transport-safety, or explicit external-protocol conversion;
- concrete controlling validation, authentication, network, and backend errors without generic replacement wrappers or user-visible stack traces;
- explicit FastAPI SQLAlchemy dependencies declared with `scope="function"` unless one streaming response intentionally owns the session while streaming, no session transport through `Request.state`, `app.state`, context variables, globals, or scoped-session registries, and separate post-handler sessions for middleware or audit work;
- strict isolation of infrastructure routes from Product data, sessions, auth context, and domain services;
- when `zitadel-developer` applies, user-resource domain logic executed against `effective_zitadel_user_id`;
- ordinary ZITADEL-backed user-resource requests requiring `effective_zitadel_user_id` to equal current authenticated `zitadel_user_id`;
- automatic generated `admin` delegation under the reserved root prefix `/for-user/{zitadel_user_id}` for every ZITADEL-backed user-resource route, with `effective_zitadel_user_id` taken from that root parameter and active-user verification before domain execution;
- access metadata and delegated-route generation remaining independent, so a role-restricted user-resource route still receives its generated delegated variant;
- no generated delegation for protected non-user-resource routes or admin routes without effective-user resource context;
- reserved root literals protected from ordinary resource-id interception;
- management `list`, `get`, and `update` constrained to the effective owner even for an admin principal, with published foreign objects exposed only through a separate minimal read-only selector boundary;
- selector access exposing neither foreign management state nor build history, credentials, logs, or another owner's mutable representation;
- owner archive/unarchive and administrator block/unblock/delete transitions for the standard mutable Product resource, with archived and blocked objects readable but otherwise immutable and delete represented as an immediately hidden soft-delete without a Product restore route;
- Product capabilities derived from the same route registry and exposed through `GET /capability`, never from a parallel policy list;
- capabilities controlling presentation but never replacing server-side authorization;
- capability keys representing Product actions or modes rather than role names;
- optional `ProductApiAction` metadata carrying exact `resource_key` and `action_key`, exposed as `product_action`, with `null` representing the deliberate absence of a UI action and uniqueness validated at route registration;
- `ApiRequestLog` policy for protected requests that reach a handler, public-route exclusion with normal method/path/request-id/status runtime logging, exact `zitadel_user_id` and `effective_zitadel_user_id`, untruncated JSON body capture, multipart/upload/binary/streaming exclusions, `/api-request-log` response-body exclusion, and credential-header redaction including `Authorization`, `Cookie`, `Set-Cookie`, `Proxy-Authorization`, `X-Api-Key`, and owner-declared secret headers;
- canonical `request_header_map` and `response_header_map` names with `dict[str, str]` semantics across ORM, database, and Product API surfaces;
- ordinary Product JSON payloads excluding raw credentials, keys, tokens, and private material;
- one explicitly declared secret-bearing response boundary with `Cache-Control: no-store`, metadata-only audit, and exclusion from body capture, cache, logs, and telemetry;
- server validation of a supplied Product request id or generation of a UUID when absent or invalid, with the canonical id stored in runtime/audit records and echoed in every Product response;
- prohibition on handwritten standard CRUD or delegated routes, client-supplied capabilities, roles, or effective owners as authorization input, and route-local bypasses around the canonical registry;
- behavior and code-contract verification that detects route bypass, missing access metadata, OpenAPI divergence, delegated-route mistakes, reserved-path interception, capability-map divergence, audit leakage, and secret-response capture.

Provider references the ZITADEL skill for identity semantics and the SQLAlchemy skill for persistence/session semantics instead of copying those contracts.

Product-specific resource actions, role allowlists beyond the standard admin-delegation boundary, selector eligibility, lifecycle guards tied to domain state, exact capability keys, Workflow/VPN/Data/Athena behavior, and concrete route paths remain in WCC design and code.

The WCC generated-client rule that creates one request id per logical operation, reuses it across the single auth-renew retry, validates the response echo, and retains it in typed client failures remains in WCC because it is client transport behavior rather than REST server behavior. This task does not create a second shared client owner for that rule.

### React Capability Consumption

`project-standards:react-ui-developer` owns the client side of the Product capability boundary:

- Product action visibility and enabled state derive only from generated backend capability metadata and row-level allowed actions;
- the typed generated contract of `GET /capability` is the only action-to-capability source, and a handwritten or parallel frontend action map and hardcoded role checks are forbidden;
- missing route access preserves the requested URL and shell and shows a concrete access error;
- personal presentation preferences are scoped to the authenticated identity; changing only `effective_zitadel_user_id` during delegation does not change them.

WCC retains its concrete resource declarations, action ordering, lifecycle UI, navigation, and standard component behavior.

### Runtime Reuse Boundary

The reusable skills own behavior and engineering constraints, while the only current concrete implementation remains in WCC. If a second real project needs the same `ProductApiRouter` runtime implementation, that change must first establish one reusable runtime owner and migrate WCC to it. Copying `backend/api_router.py`, creating a second implementation, or prematurely extracting runtime code during this task is forbidden.

## Protected Source-To-Target Ledger

### Repository `project-standards`

| Current source | Target ownership |
| --- | --- |
| `plugins/project-standards/skills/sqlalchemy-developer/references/sqlalchemy.md`, section `Table Lifecycle Ownership` | Replace the local-only rule with the three provider-owned lifecycle profiles and their semantic selection, precision, non-retrofit, external-table, and narrower-owner boundaries. |
| `plugins/project-standards/skills/kubernetes-developer/references/kubernetes.md` | Add the complete named one-off `Job` and failure-observability contract; retain the existing state classification as the canonical shared owner. |
| `plugins/project-standards/skills/rest-api-server-developer/SKILL.md` final paragraph | Remove the claim that router, role, and delegated-user semantics remain project-local; point to the expanded provider reference and the ZITADEL identity owner. |
| `plugins/project-standards/skills/rest-api-server-developer/references/rest-api-server.md` | Become the single owner of the approved Product router/resource, access, delegation, capability, OpenAPI, session, audit, request-id, secret-response, and verification contracts. |
| New `plugins/project-standards/skills/zitadel-developer/` | Own `SKILL.md`, `agents/openai.yaml`, and one detailed reusable ZITADEL reference; contain no WCC-specific names, paths, account ids, or product domain rules. |
| `plugins/project-standards/skills/react-ui-developer/references/react-ui.md` | Forbid a parallel frontend action map, define generated capability consumption, and correct preference scope from effective identity to authenticated identity. |
| `plugins/project-standards/skills/project-standardize/references/project-standardization.md` | State that actual Kubernetes and ZITADEL integrations are mechanically classified and that identifier/prose-only mentions are insufficient. |
| `plugins/project-standards/skills/project-standardize/scripts/project_standardize.py` and its behavior tests | Detect the new capability and broader Kubernetes boundaries from current real artifacts without prose/fixture false positives; keep provider discovery dynamic. |
| Plugin manifest | Publish a cache-busted plugin revision that exposes `zitadel-developer` and describes identity among the supported capability families. |

### Repository `workflow-control-center`

| Current source | Target ownership or retained local role |
| --- | --- |
| `AGENTS.md`, `Required Standards` | Add `project-standards:zitadel-developer`; retain the already applicable REST, React, SQLAlchemy, and Kubernetes selections. |
| `AGENTS.md`, section `Product API Rules` | Remove after every clause is represented by `rest-api-server-developer`, `zitadel-developer`, `react-ui-developer`, or the existing SQLAlchemy owner. No paraphrased local copy remains. |
| `AGENTS.md`, section `Database Schema Migration Specializations` | Remove after its generic named `Job`, explicit image, completion wait, log collection, and successful cleanup semantics are owned by `kubernetes-developer`. |
| `AGENTS.md`, section `Table Lifecycle Rules` | Remove after mutable product-state and append-only log semantics are owned by `sqlalchemy-developer`. |
| `AGENTS.md`, table of contents | Remove only headings deleted by this migration and preserve exact order for every remaining heading. |
| `AGENTS.md`, `Key Directory Map` entries for `backend/api/**`, `backend/api_router.py`, `backend/validate.py`, `design/backend.md`, and `design/zitadel.md` | Retain concrete WCC path ownership, but reference provider-owned reusable semantics instead of redefining them. |
| `DESIGN.md`, `Маршрутизация Требований` | Route reusable REST and ZITADEL engineering mechanics to the provider skills while retaining WCC Product API and identity-domain specializations in their existing design owners. |
| `design/backend.md`, sections `Назначение`, `Стандартный Механизм Ресурса`, `Управление И Selectors`, `Выдача Временных Учётных Данных`, `OpenAPI И Ошибки`, `Точки Расширения`, `Запрещённые Обходы`, and `Проверки` | Replace reusable REST server mechanism prose with exact provider references. Retain WCC resource policy, domain commands, exact capability keys, AWS/Data/Athena behavior, generated-client request-id behavior, and WCC verification outcomes. |
| `design/frontend.md`, reusable capability/access-control clauses | Reference `react-ui-developer` for generated capability consumption and authenticated-account preference scope; retain WCC resource declarations and concrete UI policy. |
| `design/zitadel.md`, sections `Стандартная Identity Boundary`, `Наблюдаемость OIDC`, `Локальная Персистентность`, `Current И Effective User`, `Точки Расширения`, `Запрещённые Обходы`, and `Проверки` | Replace reusable ZITADEL prose with the provider reference. Retain WCC deployment, Product/Secret/S3 behavior, account-menu presentation, local persistence paths, and `state-smoke` acceptance. |
| `design/observability.md`, section `Retention И Устойчивость` | Replace the stale local state-contract reference with `project-standards:kubernetes-developer`; retain GlitchTip-specific lifecycle and deployment facts. |
| `docs/local_kubernetes.md`, section `Локальное Состояние` | Replace the stale local state-contract reference with `project-standards:kubernetes-developer`; retain the concrete WCC path classification and operations. |
| `backend/**`, `ui/**`, `model_sqlalchemy/**`, deployment manifests, and tests | Runtime behavior remains unchanged. Existing tests remain executable evidence of the behavior whose instruction owner moved. |

Within the removed `Product API Rules` section, ownership is exhaustive:

- backend placement, router/resource registration, route kinds, access declarations, OpenAPI, canonical fields, concrete errors, session ownership, infrastructure separation, `/for-user/**`, capabilities, request audit, secret responses, reserved paths, and route-test obligations move to `rest-api-server-developer`;
- token introspection, active ZITADEL identity, current/effective identity, roles from the current request, and active delegated-user validation move to `zitadel-developer`;
- Product action visibility and the prohibition on role-based frontend policy move to `react-ui-developer`;
- SQLAlchemy session and persisted-field details continue to reference `sqlalchemy-developer` rather than becoming a second REST owner.

### Repository `marketplace-tr-priority`

| Current source | Target ownership or retained local role |
| --- | --- |
| `AGENTS.md`, section `Table Lifecycle Rules`, profile mechanics | Move to the shared `refreshable snapshot lifecycle` in `sqlalchemy-developer`. |
| `AGENTS.md`, section `Table Lifecycle Rules`, project default selection | Retain one concise project-local section named `Table Lifecycle Selection` stating that stable relational business-data uses the shared refreshable-snapshot profile by default unless a narrower owner selects another profile. |
| `AGENTS.md`, table of contents | Replace `Table Lifecycle Rules` with `Table Lifecycle Selection` in exact remaining order. |
| ORM models, scripts, database state, and tests | No schema, data, timestamp, read-filter, refresh, stale-finalization, or runtime behavior change. |

### Other Repositories

No tracked change is required solely because a repository contains a foreign `zitadel_user_id` value or prose reference. `project-standardize --check` determines changes from real current artifacts after the classifier update. Any newly detected missing standard is changed only when the detection evidence is a real applicable technology boundary and the repository has no explicit user-authorized exception.

## Classifier Contract

Automatic selection remains mechanical selection into `Required Standards`, not runtime loading magic.

Kubernetes positive evidence includes real Kubernetes resource manifests, Helm chart or template structure, Kustomize configuration, or executable use of a Kubernetes client. Detection is not limited to five resource kinds. Documentation prose and string fixtures remain negative evidence.

ZITADEL positive evidence includes actual ZITADEL deployment/configuration structure, a dedicated ZITADEL client boundary, or executable OIDC configuration explicitly bound to ZITADEL. A generic OIDC dependency alone does not prove ZITADEL, and a `zitadel_user_id` identifier, path component, payload field, proxy name, or test fixture alone remains negative evidence.

The classifier continues to:

- discover available provider skills dynamically;
- fail closed when an applicable provider is unavailable;
- write only missing `Required Standards` entries;
- preserve protected consumer prose;
- require an approved source-to-target ledger before protected instruction mutation;
- avoid repository-name and absolute-workspace allowlists.

## Failure Handling And Recovery

- If the new provider skill is unavailable in the installed plugin, consumer mutation stops before adding its `Required Standards` entry.
- If classifier evidence is ambiguous, the repository is not mutated automatically; semantic audit resolves whether the technology is actually present.
- If a protected consumer clause has no proven target owner, that clause remains in place and the migration remains incomplete.
- If provider and consumer contracts conflict, the conflict is fixed at the correct stable owner; no compatibility paragraph or duplicated exception is introduced.
- A failed plugin validation, provider test, classifier test, consumer-required verification, plugin install, workspace check, commit, or push keeps the goal active.
- Existing runtime code and data provide the recovery boundary: because this task changes ownership and classification rather than runtime behavior, rollback never requires a database or route compatibility layer.

## Verification Design

### Provider And Plugin

The `project-standards` repository verification contract applies. Acceptance requires:

- complete plugin validation through `python ~/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py plugins/project-standards`;
- skill validation for every changed skill and the new `zitadel-developer` skill through `python ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py <skill-root>`;
- provider `pytest -q`;
- direct `plugins/project-standards/skills/project-standardize/scripts/project_standardize.py --help`;
- targeted classifier behavior covering:
  - existing FastAPI and SQLAlchemy selection;
  - Kubernetes resource manifest selection;
  - Helm selection;
  - Kustomize selection;
  - executable Kubernetes-client selection;
  - actual ZITADEL deployment/client selection;
  - missing new provider detection;
  - documentation and string-fixture false positives;
  - a browser-runtime-style `{zitadel_user_id}/{vpn_config.name}` or equivalent identifier-only false positive;
  - preservation of protected instruction overlays in write mode.

The plugin manifest version is cache-busted through the canonical plugin update workflow. The installed local plugin must resolve the same new version and expose `project-standards:zitadel-developer` before consumer handoff.

Workspace semantic acceptance follows `project-standards:project-standard-audit`; a passing classifier is only mechanical evidence.

### Consumer Semantics

Repository `workflow-control-center` requires:

- semantic reread of `AGENTS.md`, `DESIGN.md`, `design/backend.md`, `design/frontend.md`, `design/zitadel.md`, `design/observability.md`, and `docs/local_kubernetes.md` together with the provider references;
- proof that every removed reusable clause has exactly one provider owner and that every retained clause is WCC-specific;
- no stale reference to a local `Local Kubernetes State Contract`;
- no local duplicate of the shared lifecycle, REST, ZITADEL, Kubernetes, or frontend capability contracts;
- existing Product API route/capability, audit, and ZITADEL behavior verification through `pytest test/backend/test_product_api.py test/backend/test_product_capability_api.py test/backend/test_api_request_log.py test/backend/test_zitadel_user.py test/code/test_backend_route_registration_contract.py -q`;
- inspection of the existing ZITADEL backend and UI test ownership to confirm that migrated requirements retain executable coverage;
- no UI build, Kubernetes apply, browser suite, or runtime deployment solely for documentation/instruction changes, unless implementation unexpectedly changes `ui/**`, user-visible behavior, deployment state, or code, in which case the full WCC changed-artifact handoff contract applies.

Repository `marketplace-tr-priority` requires:

- semantic reread of the retained profile selection with the provider lifecycle profiles;
- proof that the refreshable-snapshot semantics and precision are unchanged;
- no database or Python verification solely for the instruction ownership migration, unless implementation changes code, tests, runtime behavior, or database behavior, in which case the repository handoff contract applies.

### Workspace Acceptance

After the provider is installed and consumer instructions are migrated:

- run `plugins/project-standards/skills/project-standardize/scripts/project_standardize.py --workspace-root ~/Projects --check` from repository `project-standards`;
- require every currently discovered repository to be valid;
- semantically inspect every repository newly classified for Kubernetes or ZITADEL and reject false-positive selection;
- require WCC to declare `zitadel-developer`;
- require identifier-only consumers such as `browser-runtime` not to declare `zitadel-developer` unless their actual current artifacts independently justify it;
- inspect all current `AGENTS.md` files for duplicated shared lifecycle, Product API, `/for-user/**`, capability, Kubernetes state, and ZITADEL engineering prose;
- confirm all changed tracked repositories are committed in logical commits, pushed, clean, and have local `HEAD` equal to `origin/main`.

### Terminal Completion Audit

After presumed completion, audit the complete current scope again from scratch against this specification, its paired goal, every referenced stable provider and consumer contract, the current repositories, installed plugin state, classifier output, and remote Git state. Fix every incomplete, contradictory, duplicated, stale, falsely classified, unavailable, unverified, uncommitted, or unpushed finding, rerun affected verification, and repeat a new full audit until one full post-fix audit finds none.

Do not generate a separate completion ledger, evidence document, or proof artifact. The audit/fix cycle operates directly on current contracts and current system state.
