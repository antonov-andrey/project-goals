# Shared Verification And Code-Contract Ownership

## Required Outcome

`project-standards` becomes the single reusable owner of generic handoff verification, the strictly eligible executable subset of mechanical standard checks, shared exact Git repository-scope resolution, and pytest owner-local suite discovery. A mechanical check exists only for one independently normative closed predicate that its deterministic implementation decides completely over its declared scope. Every other reusable requirement remains with its capability skill and is verified through mandatory complete semantic review; reusable runtime behavior tests live with their runtime-owning submodule, and exact Product and consumer-integration checks remain in the consumer repository.

`workflow-control-center` and `marketplace-tr-priority` stop carrying forked copies of shared `test/code/**`, `test/lib/**`, root pytest discovery, and Python scope tooling. Both consumers use one exact-commit-pinned development tooling distribution and retain only genuine project overlays and exact local verification. Product runtime behavior, persisted data, schemas, external state, UI behavior, and production dependency surfaces do not change.

## Coordinating Repository And Document Mode

The coordinating repository is `project-standards` because it owns the generic verification contracts, exact-checker eligibility and orchestration, mandatory semantic acceptance contract, shared pytest plugin, and installable development tooling distribution.

This pair uses a dedicated implementation specification because the change spans the provider, two protected consumers, four reusable submodule providers, the marketplace capability plugin, dependency boundaries, executable checker protocol, test ownership migration, Git submodule publication, and cross-repository acceptance.

The paired task artifacts are:

- `.spec/2026-07-26-verification-and-code-contract-ownership-spec.md`;
- `.spec/2026-07-26-verification-and-code-contract-ownership-goal.md`.

They remain ignored and untracked through the coordinating repository's root `.gitignore`; no one equivalent ignore-pattern spelling is required. They are never deleted without an explicit user request.

## Verified Current State

- The previous persistent goal is complete; no unfinished goal owns this task pair.
- Repository `workflow-control-center` currently has 56 files and 12,578 lines under root `test/code/**`.
- Repository `marketplace-tr-priority` currently has 51 files and 11,466 lines under root `test/code/**`.
- The two consumers share 47 test basenames. Of those, 27 files are byte-identical and 20 are consumer forks of substantially the same mechanical contract.
- Their root `test/lib/**` trees share 13 basenames: seven are byte-identical and six are forked variants.
- Both consumers carry their own root `conftest.py`, pytest suite-discovery helpers, Python scope resolver, AST/source-analysis helpers, dead-code and visibility integrations, naming helpers, script-contract helpers, and submodule-dispatch tests.
- `project-standards:python-developer` already demonstrates the intended owner model: checker scripts and their behavior tests live under the owning skill, with shared checker mechanics below that skill.
- `project-standardize` already parses `Required Standards`; creating another selected-standard map would duplicate the canonical project selection owner.
- `BaseModelStrict` runtime semantics are owned by repository `submodule-base-model`, `DESIGN.md`; `OrmBase` runtime semantics are owned by repository `submodule-base-model-sqlalchemy`, `DESIGN.md`. Their runtime behavior is currently duplicated by both consumers in `test_validated_object_runtime_contract.py`.
- `openai_cached` is pinned by both consumers to the same provider revision. Its stable public and host contract is now routed through `openai_cached/DESIGN.md`, while both consumers still carry the same host-side OpenAI SDK ban test.
- `ozon_seller_api/DESIGN.md` already owns the reusable Ozon Seller API host boundary, while `marketplace-tr-priority` carries its mechanical host-boundary scan.
- WCC and MTP `requirements.txt` currently mix Product/runtime packages with test and code-analysis packages.
- WCC `backend/Dockerfile` installs root `requirements.txt`; therefore adding `project-standards` to that file would incorrectly make development governance tooling a Product runtime dependency.
- Both local environment installers currently recreate `.venv` and install only `requirements.txt`.
- Neither consumer has `requirements-dev.txt`.
- `project-standards` currently has no installable Python distribution, no `project-standard-check` console command, and no shared pytest plugin.
- `agent-plugins` intentionally exposes no Python distribution. This task does not change that boundary.

## Approved Mechanical And Semantic Correction

The earlier migration treated many selected static scans as mechanical proof of broad semantic rules. That model is rejected. This correction is part of the required outcome and replaces every later source-ledger or verification statement that assigns an approximate source scan to a checker:

- scripts may verify only independently normative closed predicates with complete deterministic algorithms over their whole declared scope;
- name, path, suffix, framework, threshold, signal, exception, and allowlist heuristics are forbidden unless the normative rule itself defines that exact exhaustive set;
- a partial detector for one broad rule is removed rather than relabeled as partial mechanical evidence;
- checker output, tests, changed files, implementation plans, historical findings, and Git history never define or narrow semantic coverage;
- direct writing workflows and explicit audit workflows derive the complete applicable owner-requirement inventory independently, assess every requirement semantically, and restart the whole semantic pass after every fix;
- validators may prove report structure and exact parent-supplied inventory presence, but never semantic truth, evidence quality, or correctness of a verdict;
- semantic acceptance after the last fix is artifact-free unless the user explicitly requests a structured audit report;
- the current exact provider checker set contains only the repository-wide `.sh` artifact prohibition; future checkers must satisfy the same eligibility contract;
- the approximate `openai_cached` and `ozon_seller_api` host checkers are removed. Their stable host contracts remain in provider `DESIGN.md` and are reviewed semantically in each applicable consumer.
- consumer-local `test/code/**` is subject to the same eligibility rule: historical path allowlists and JSX token or regular-expression scans are removed instead of being retained as Product-specific proof; the Product contracts remain in WCC design owners and complete semantic review.

## Scope

The change includes:

- provider-owned generic handoff and evidence contracts;
- replacement of copied stable Ozon host restrictions in `agent-plugins` with an exact provider-design reference;
- one installable `project-standards` development and test tooling distribution;
- one `project-standard-check` read-only console interface;
- exact-checker eligibility, capability manifests only for eligible predicates, shared checker runtime, and provider tests;
- discovery of strictly eligible exact host checkers from exact checked-out Git submodules;
- one explicit shared pytest plugin for owner-local suite discovery;
- deletion of duplicated weak static scans and helper code from WCC and MTP after their normative meaning is routed to its semantic owner;
- relocation of reusable validated-object runtime tests to `submodule-base-model` and `submodule-base-model-sqlalchemy`;
- semantic host-conformance ownership in `submodule-openai-cached` and `submodule-ozon-seller-api` `DESIGN.md` without approximate host checkers;
- development/runtime requirement separation in WCC and MTP;
- removal of obsolete consumer Python scope tooling and discovery-only root `conftest.py`;
- exact consumer overlays and genuinely consumer-local code-contract tests;
- provider and submodule publication, consumer exact-commit or exact-gitlink updates, plugin validation/reinstallation, acceptance, commits, and pushes.

## Non-Goals

- Do not move Product runtime code into `project-standards`.
- Do not make `project-standards`, checker dependencies, or pytest tooling a Product image or production runtime dependency.
- Do not turn `agent-plugins` into a Python distribution.
- Do not copy checker code into generated consumer files, consumer packages, plugin caches, or sibling checkouts.
- Do not create a second `Required Standards` registry, handwritten action map, compatibility runner, compatibility import, or legacy test bridge.
- Do not mechanically interpret instruction prose as executable policy.
- Do not replace semantic review with checker success.
- Do not keep or add a checker that detects only selected likely cases of one broader semantic rule.
- Do not move exact WCC Product paths, WCC UI component contracts, MTP interactive prompt integration, or another consumer-specific restriction into a generic provider.
- Do not change Product behavior, public APIs, database schema or data, Kubernetes state, ZITADEL state, AWS state, external marketplace state, or UI presentation.
- Do not perform production database writes or production-bound external mutations.
- Do not delete any `.spec` file.

## Approved Integration Model

The durable checker, scope, diagnostic, pytest plugin, test-family, distribution, exact-pin, and Product-runtime isolation contracts are owned by `project-standards/DESIGN.md` and the capability references named in the source ledger below. This specification does not create alternate interfaces or exceptions.

The task-specific integration decisions are:

- WCC and MTP adopt the provider distribution and explicit pytest plugin before removing consumer copies.
- In each consumer, `requirements-dev.txt` begins with `-r requirements.txt`, then declares only development/test dependencies and the exact provider pin; the normal local installer validates and installs that one development requirements file. Product install paths continue to install only `requirements.txt`.
- WCC keeps `hypothesis` and related packages in Product requirements while current workflow-source runtime imports them.
- The old consumer scope CLI and duplicated suite discovery are removed without wrappers after all callers use the provider owners.
- Consumer helpers move or disappear only after real use-site inspection; exact consumer fixtures remain local.
- Exact provider checker tests use isolated synthetic repositories, reusable runtime tests execute the real owning submodule, retained consumer checks exercise exact Product integration, and complete semantic acceptance independently covers every remaining applicable owner requirement.

### Consumer Verification Overlays

WCC retains the exact sequence owned by its `AGENTS.md`, section `Project Verification Rules`; reusable browser and Kubernetes verification remain in their selected provider skills. MTP retains only the production verification specialization owned by its own `AGENTS.md`, section `Project Verification Rules`. This task adds no second copy or alternate sequence.

## Complete Source-To-Target Ledger

### Repository `project-standards`

| Current source | Target ownership |
| --- | --- |
| `AGENTS.md` and `DESIGN.md` | Retain the approved development/test distribution boundary, checker ownership, runner role, exact-commit consumer pin, and prohibition on Product runtime coupling. |
| `project-foundation/references/execution.md` | Own generic evidence selection, semantic review, structural verification, failure remediation, and honest command reporting. |
| `project-foundation/references/writing-and-reporting.md` | Retain the existing single semantic owner of unambiguous instruction language; remove the approximate literal scan and its owner-path exception. |
| `pytest-developer/references/test.md` | Own ordinary suite, targeted suite, local `test/code`, provider checker tests, submodule behavior tests, and explicit pytest plugin semantics. |
| `project-instruction-developer/references/skill-model.md` | Own capability checker manifest and plugin support-owner placement. |
| `submodule-developer/references/submodule-model.md` | Own reusable submodule behavior-test placement, exact host-checker eligibility, and semantic host-conformance ownership. |
| `python-developer/references/python-core.md` | Own the exact Black formatting gate before handoff Python test execution. |
| `react-ui-developer/references/react-ui.md` | Own reusable current-assets browser, complete workflow/recovery, stale state remediation, and screenshot comparison semantics. |
| `kubernetes-developer/references/kubernetes.md` | Own deployment of current assets and repair of normal reconciliation instead of force-only verification. |
| `project-standard-audit` | Run the complete eligible mechanical subset as separate evidence and independently audit every applicable normative requirement semantically. |
| Existing `project-standardize` Required Standards parser | Become the one reusable parser owner consumed by both classification and checker discovery; no second parser or selected-standard map remains. |
| New installable tooling distribution | Own the canonical source package under `plugins/project-standards/lib/project_standards/`, console entry point, explicit pytest plugin, project/scope model, manifest loading, process protocol, deterministic diagnostics, and direct packaging of provider-owned checker assets without generated source copies. |
| Capability skill roots | Own normative contracts and mandatory semantic review. They own `checker.toml`, checker scripts, and checker behavior tests only for independently normative closed predicates with complete decision procedures. |
| Provider tests | Cover package installation, runner protocol, exact Git scope, manifest selection, diagnostics, pytest discovery, and every retained exact checker branch without claiming semantic coverage. |

### Shared Consumer Tests: Python And Repository Contracts

The following current basenames exist in both WCC and MTP unless noted. Their duplicated consumer implementations are removed after semantic classification routes the normative meaning to its canonical owner, exact runtime behavior receives real behavior tests, and only independently normative closed predicates retain an executable checker.

| Current source basenames | Target owner and required preserved meaning |
| --- | --- |
| `test_python_black_format.py` | `python-developer` exact Black handoff command after semantic scope selection; formatting success proves only that explicitly selected scope. |
| `test_python_class_necessity_contract.py`, `test_python_collection_carrier_naming_contract.py`, `test_python_contracts.py`, `test_python_dead_code_contract.py`, `test_python_docstring_standard.py`, `test_python_file_layout_contract.py`, `test_python_import_cycle_contract.py`, `test_python_method_binding_contract.py`, `test_python_method_visibility_contract.py`, `test_python_multiline_string_contract.py`, `test_python_naming_contract.py`, `test_python_naming_contract_helpers.py`, `test_python_non_module_imports.py`, `test_python_package_alternative_constructor_contract.py`, `test_python_package_exports.py`, `test_python_plural_token_naming_contract.py`, `test_python_proxy_wrapper_contract.py`, `test_python_signature_contract.py`, `test_python_signature_truthfulness_contract.py`, `test_python_tuple_carrier_contract.py`, `test_python_use_scope_ownership_contract.py`, `test_python_visibility_contract.py` | `python-developer` normative semantic owner. Delete selected-name, selected-path, threshold, approximate AST, exception-list, and heuristic checkers and their tests. Complete semantic review covers every applicable rule without consumer allowlists. |
| `test_validated_object_constructor_contract.py`, `test_validated_object_contract.py`, `test_validated_object_field_order.py` | `python-developer` and `sqlalchemy-developer` semantic contracts plus the real runtime behavior tests owned by `base_model` and `base_model_sqlalchemy`; do not replace them with approximate static scans. |
| `test_main_project_import_boundary_contract.py` | `project-foundation` semantic owner; Main-project and Legacy classification is semantic and MUST NOT be inferred by a path/name checker. |
| `test_python_project_structure.py` | `project-foundation` and `project-instruction-developer` semantic owner boundary; delete hardcoded WCC/MTP path-set approximations. |
| `test_python_code_scope_resolve.py` | Provider scope-runtime behavior tests. Remove the consumer CLI and its regression test after every scope branch is covered centrally. |
| `test_no_shell_scripts.py` | `python-cli-developer` exact repository-wide `.sh` artifact checker and its owner-local behavior tests. |
| `test_python_script_contract.py` | `python-cli-developer` semantic owner plus direct executable behavior verification; delete filename/path/source-pattern approximations. |
| `test_python_submodule_portability.py`, `test_python_submodule_script_contract.py` | `submodule-developer` plus `python-cli-developer` semantic ownership and direct standalone/host launch behavior tests; delete path/name approximation checkers. |
| `test_python_submodule_structure.py` | Shared pytest plugin and submodule-owned test execution; remove consumer subprocess dispatch rather than wrapping it. |
| `test_pytest_suite_discovery.py`, `test_test_import_and_support_artifact_contract.py` | `pytest-developer` plugin behavior tests for exact discovery mechanics; test-support ownership and import semantics remain mandatory semantic requirements without an approximate consumer scanner. |
| `test_script_catalog_contract.py` | `project-documentation-developer` semantic owner; delete the language-, heading-, and path-pattern approximation. |
| MTP-only `test_markdown_links.py` | `project-documentation-developer` semantic owner; delete the incomplete regex Markdown parser rather than presenting selected link forms as complete documentation validity. |
| MTP-only `test_reference_policy.py` | Route each normative meaning to `project-instruction-developer`, `project-foundation`, or `python-cli-developer` semantic ownership; delete selected-token and owner-path-exception checkers. |

### Shared Consumer Tests: SQLAlchemy

| Current source basenames | Target owner and required preserved meaning |
| --- | --- |
| `test_model_sqlalchemy_column_order.py`, `test_model_sqlalchemy_foreign_key_ban.py`, `test_model_sqlalchemy_nullability_contract.py`, `test_model_sqlalchemy_row_method_shape.py`, `test_model_sqlalchemy_typed_field_contract.py`, `test_model_sqlalchemy_used_table_collection_column_naming_contract.py` | `sqlalchemy-developer` semantic owner. Delete ORM discovery, class-shape, name, and selected-AST approximation checkers; validate real ORM behavior through owner-local behavior tests where behavior is executable. |
| WCC-only `test_model_sqlalchemy_standard_field_contract.py`, `test_model_sqlalchemy_standard_index_contract.py` | `sqlalchemy-developer` shared semantic field/lifecycle owner; do not retain WCC constants as checker configuration or inference lists. |
| `test_python_project_table_bootstrap_contract.py`, `test_sqlalchemy_session_contract.py` | `sqlalchemy-developer` complete semantic owner plus direct transaction/session behavior tests; delete source-pattern scans. |
| ORM-related assertions currently mixed into validated-object static tests | Route to the same SQLAlchemy semantic owner and real runtime tests without a second static-checker copy. |

### Shared Consumer Tests: Runtime And Host Providers

| Current source | Target owner and required preserved meaning |
| --- | --- |
| Both `test_validated_object_runtime_contract.py`, `BaseModelStrict` tests | Repository `submodule-base-model`, local behavior tests. Preserve strict construction, extra rejection, assignment/default validation, strict `model_validate`, and constructor-validating copy-with-overrides. |
| Both `test_validated_object_runtime_contract.py`, `OrmBase` tests | Repository `submodule-base-model-sqlalchemy`, local behavior tests. Preserve constructor/assignment validation, canonical normalizers/default factories, missing/null/type failures, forbidden default shapes and metadata, and no revalidation of DB-loaded rows. |
| Both `test_python_openai_sdk_ban.py` | Repository `submodule-openai-cached`, `DESIGN.md` semantic host contract. Delete the selected-call/name host checker and audit every host requirement semantically. |
| MTP-only `test_python_ozon_seller_api_boundary.py` | Repository `submodule-ozon-seller-api`, `DESIGN.md` semantic host-integration contract. Delete the URL/name/prefix/pagination-signal checker and audit public integration, transport, pagination, and configuration ownership semantically. |

### WCC REST And Product Checks

| Current source | Target ownership or retained local role |
| --- | --- |
| `test_backend_route_registration_contract.py`, direct FastAPI/APIRouter bypass analysis and its synthetic checker test | `rest-api-server-developer` complete semantic owner; delete the selected-constructor/source-pattern checker. |
| `test_backend_route_registration_contract.py`, real WCC app/registry equality and absence of forbidden Product users routes | Retain as WCC ordinary backend behavior tests, not shared static checker tests. |
| `test_product_api_resource_contract.py` | Move generic route/resource requirements to `rest-api-server-developer` semantic ownership; retain real WCC behavior tests and only genuinely WCC-specific exact integration checks. |
| `test_backend_module_structure_contract.py` | Delete the historical canonical/forbidden path allowlist and import-fragment scan. Current WCC backend ownership stays in `Key Directory Map`, `design/backend.md`, and semantic review. |
| `test_product_api_name_validator_contract.py` | Delete the suffix list, inferred ORM lookup, skipped unmatched API models, and nonempty-sample assertion. Canonical ORM/API field and validator behavior remains covered by WCC backend behavior tests and complete semantic review of `design/persistence.md` and the Product API owner. |
| `test_project_dependency_contract.py` | Delete the selected requirement-token scan. Product PostgreSQL behavior remains covered by backend/runtime tests, while complete dependency correctness and Product-image isolation are reviewed from actual imports, requirements, and Docker inputs. |
| `test_ui_error_alert_contract.py`, `test_ui_form_field_contract.py` | Delete JSX string and regular-expression scans because they recognize selected syntax rather than completely deciding the shared-component contract. `design/frontend.md`, component behavior tests, the WCC browser sequence when UI changes, and complete semantic review retain the contract. |

### MTP Exact Local Check

| Current source | Retained local role |
| --- | --- |
| `test_python_interactive_prompt_contract.py` | Delete the partial `input()` and selected-import scan. Real prompt behavior remains covered by `test/test_cli_prompt.py`; complete use of the canonical `lib/cli/prompt.py` owner and absence of bypasses are reviewed semantically across the actual Product and tool scope. |

### Consumer Helper And Tool Cleanup

| Current source family | Target |
| --- | --- |
| Common `test/lib/model_sqlalchemy_contract.py`, `no_shell_scripts.py`, `pytest_suite_discovery.py`, `python_code_contracts.py`, `python_code_scope_helpers.py`, `python_dead_code_vulture.py`, `python_method_visibility_jedi.py`, `python_naming_contract.py`, `python_pattern_scan.py`, `python_script_contract.py`, `python_submodule_script_contract.py`, and `repo_helpers.py` | Keep only exact reusable Git scope, checker protocol, pytest discovery, and `.sh` predicate mechanics under their provider owners. Delete heuristic analysis and exception-list algorithms rather than moving them. |
| MTP `test/lib/project_instruction_assets.py` | Delete selected instruction-token and path approximation logic; retain only exact parser mechanics with their real provider owner when independently required. |
| WCC `test/lib/project_environment.py` | Remove with generic code-scan callers; retain no compatibility helper. |
| WCC `test/lib/backend_config.py`, `workflow_input.py`, and `ui_browser/**` | Retain as WCC-local Product/backend/browser fixtures while real WCC behavior tests use them. |
| MTP `test/lib/openai_cached_sqlite.py`, `ozon_sales_model_api.py`, and `shared.py` | Retain as MTP-local Product test support while their current OpenAI profile and sales-model behavior tests use them. |
| MTP `test/test_repo_helpers.py` | Split by real owner. Move `relpath_normalize` and Git-ignore filtering cases with `test/lib/repo_helpers.py` into provider runtime tests. Retain only tests for consumer-owned members that still remain in MTP `tool/lib/repo.py`; remove tests for generic-only members that move to the provider. The retained test MUST NOT import the deleted consumer `test/lib/repo_helpers.py`. |
| WCC `test/lib/shared.py` | Delete after confirming its current absence of real use sites; do not move an unused identical file into a provider. |
| WCC/MTP `tool/python_code_scope_resolve.py` and `tool/lib/python_code_scope.py` | Delete after provider scope runtime is active and every call site is migrated. No compatibility wrapper remains. |
| WCC/MTP `tool/lib/repo.py` | Retain only members still used by consumer-owned tools after scope/checker migration. Move generic checker-only Git-root, ignore, and path mechanics into the provider runtime, migrate their tests, and delete consumer-local dead members without replacing them with provider imports from Product runtime paths. |
| WCC/MTP root `conftest.py` and copied suite-discovery helper | Delete because both currently own discovery only; enable the explicit provider plugin in pytest configuration. |

### Repository `workflow-control-center`

- Keep the stable `Project Verification Rules` overlay and provider references already approved.
- Split `requirements.txt` and `requirements-dev.txt` by proven runtime use.
- Update the local venv installer and its tests to install the development set reproducibly.
- Keep `backend/Dockerfile` on base Product requirements only and verify its dependency digest does not include development-only inputs.
- Enable the explicit pytest plugin and remove discovery-only consumer copies.
- Migrate the complete ledger without losing WCC exact Product checks.
- Update `Key Directory Map`, dependency matrix, test/tool descriptions, and directly affected design/docs only after the corresponding current artifacts actually move or disappear.
- Update gitlinks for `base_model`, `base_model_sqlalchemy`, and `openai_cached` only to published provider commits that passed standalone tests.

### Repository `marketplace-tr-priority`

- Keep the stable `Project Verification Rules` overlay and provider references already approved.
- After publishing and updating `openai_cached`, replace the copied root `openai_cached` host contract with only its root path and exact `openai_cached/DESIGN.md` host-contract reference.
- Split `requirements.txt` and `requirements-dev.txt` by proven runtime use.
- Update the local venv installer and its tests to install the development set reproducibly.
- Enable the explicit pytest plugin and remove discovery-only consumer copies.
- Migrate the complete ledger while retaining real interactive-prompt behavior tests and any helper still used by real Product behavior tests; owner use and bypass absence remain semantic requirements.
- Update `Key Directory Map`, dependency matrix, test/tool descriptions, and directly affected documentation only after corresponding artifacts actually move or disappear.
- Update gitlinks for `base_model`, `base_model_sqlalchemy`, `openai_cached`, and `ozon_seller_api` only to published provider commits that passed standalone tests.

### Submodule Provider Repositories

- `submodule-base-model` receives the reusable `BaseModelStrict` runtime behavior tests and any minimal standalone pytest configuration needed by its existing public contract.
- `submodule-base-model-sqlalchemy` receives the reusable `OrmBase` runtime behavior tests and preserves standalone execution with its real dependencies.
- `submodule-openai-cached` retains `DESIGN.md` as stable public/host semantic owner, removes the approximate host-conformance manifest/checker/tests, and removes the obsolete `ARCHITECTURE.md` identity.
- `submodule-ozon-seller-api` retains `DESIGN.md` as host semantic owner and removes the approximate host-conformance manifest/checker/tests.
- Each provider is committed, pushed, and verified before consumers update gitlinks.

### Repository `agent-plugins`

- Keep `agent-plugins` outside the Python distribution boundary.
- Keep the marketplace capability skill as the task-facing integration owner, but reference `ozon_seller_api/DESIGN.md`, section `Host integration`, for the stable provider boundary and mandatory semantic host review instead of copying those restrictions into the skill.
- Validate and publish the changed plugin repository after the provider design owner is published.

## Migration And Compatibility

- This is one coordinated steady-state migration, not a compatibility period.
- Provider package and submodule replacements must exist and pass standalone verification before a consumer copy is removed.
- Consumers then pin the exact provider commit and exact submodule revisions, enable the plugin and eligible exact checkers, and remove replaced local files in the same consumer change.
- If provider behavior does not cover one current consumer assertion, the assertion is classified semantically:
  - reusable stable behavior expands the real provider contract and real behavior test when executable;
  - exact consumer behavior remains consumer-local;
  - obsolete behavior is removed only when its stable owner no longer requires it and semantic review proves no contract loss.
- No skipped test, xfail, allow-all path, stale wrapper, copied fixture package, legacy command alias, or dual discovery path is an accepted bridge.
- Existing Product runtime requirements stay available. Dependency cleanup cannot remove a package used by current Product code or runtime tooling.
- A provider fix after one consumer pin creates a new provider commit; every affected consumer pin is updated to that final exact commit before acceptance.
- `.spec` files remain ignored, untracked, and retained.

## Verification Design

### Provider Distribution And Runner

Provider automated tests must cover:

- installation into one clean temporary Python environment from the repository package without access to a sibling checkout or plugin cache;
- one canonical library source shared by plugin scripts and the distribution, plus exactly one installed copy of every manifest/checker asset with source-identical content and preserved owner-relative identity;
- console help and exact mandatory arguments;
- selected capability discovery through `Required Standards`;
- absence of a second selection map;
- exact version-1 capability and submodule manifest parsing, owner validation, Git-wildmatch path selection, and rejection of unknown or escaping values;
- exact JSON stdin request plus JSON Lines finding protocol, optional-line omission, output/exit consistency, and execution through the current interpreter without a shell;
- one successful selected checker;
- one deterministic conformance finding and exit code `1`;
- invalid project root, invalid/duplicate manifest, missing checker script, malformed checker output, and subprocess failure with exit code `2`;
- unselected checker exclusion;
- `changed` staged, unstaged, rename, delete, and untracked paths;
- graph/full-on-change behavior;
- `all` scope;
- clean changed scope;
- direct submodule dirty state and changed gitlink handling;
- deterministic checker and finding order;
- target worktree non-mutation;
- continued collection of other checker results after one finding or execution error.

### Exact Checker Eligibility And Semantic Coverage

For every migrated code-contract family:

- classify the normative owner and the complete requirement before considering implementation;
- retain a checker only when the rule is independently normative, closed, and decided completely by one deterministic algorithm across the whole declared scope;
- for each retained checker, test one valid sample, the primary forbidden sample, every critical exact edge, path diagnostics, and appropriate `changed` and `all` behavior;
- forbid consumer-name/path assumptions, inferred ownership, selected examples, thresholds, smell signals, false-positive suppressions, and exception allowlists unless the normative contract itself defines that exact exhaustive set;
- delete an ineligible source scan and its tests instead of moving, weakening, or relabeling it;
- preserve the underlying normative meaning at its canonical provider or Product owner and verify every applicable requirement through a complete semantic pass independent of checker and test results.

Passing provider tests is only mechanical evidence. Before deleting each source family, inspect its actual assertions and route every stable meaning to an owner; after all fixes, repeat the complete semantic audit from owner discovery until a fresh pass has no finding and no uncovered requirement.

### Pytest Plugin

Provider tests must prove:

- root, tracked project-local Skill, and direct-submodule owner-local test discovery;
- standalone submodule execution remains valid;
- ignored worktrees, venvs, caches, `.spec`, build outputs, and unrelated nested repositories are not collected;
- explicit `--ignore=test/code` remains effective;
- discovery output is deterministic;
- a consumer-local root `conftest.py` with real fixtures can coexist without duplicating discovery;
- plugin absence fails clearly in a consumer configured to require it.

### Submodule Behavior And Semantic Host Conformance

Run each changed submodule's standalone pytest suite. Required observable branches are:

- `BaseModelStrict`: valid construction/copy and the constructor coercion, extra-field, assignment, invalid-default, strict validation, and invalid override failures;
- `OrmBase`: valid constructor/assignment/default/normalizer behavior and type, null, missing, forbidden default, unsupported metadata, and DB-load non-revalidation branches;
- `openai_cached`: standalone runtime behavior tests only; its public integration, direct SDK use, generic-base use, profile ownership, and orchestration host requirements are reviewed semantically from `DESIGN.md`;
- `ozon_seller_api`: standalone provider behavior tests only; its public integration, transport, internal import, pagination, and configuration host requirements are reviewed semantically from `DESIGN.md`.

Run `project-standard-check --project-root <consumer-root> --scope all` only for eligible exact provider or Submodule predicates. Independently include every applicable Submodule host-contract requirement in the consumer semantic audit.

### Consumer Acceptance

For both WCC and MTP:

- recreate or refresh the local Python 3.14 environment through the repository's normal installer and prove both base and exact-pinned development dependencies are present;
- prove the environment does not resolve `project-standards` from a sibling checkout, editable path, `main`, or plugin cache;
- run Black on changed Python scope before Python tests;
- run `project-standard-check --project-root <consumer-root> --scope all`;
- run every retained consumer `test/code/**` check when the current consumer has an independently normative exact local predicate; neither consumer retains one after this corrected classification;
- run `pytest --ignore=test/code -q`;
- derive every applicable provider, Submodule, Product, and project-overlay requirement independently of those commands and complete one fresh semantic pass after the last fix;
- inspect pytest collection output to prove the consumer root, tracked project-local Skill, and direct-submodule owner-local test roots are collected once, the installed `project-standards` provider tests are not collected as consumer suites, and no old discovery path remains;
- verify every removed helper/tool/conftest has no remaining import or instruction reference;
- verify `requirements.txt` still contains every package imported by Product runtime and excludes the installed governance package;
- verify `requirements-dev.txt` begins with `-r requirements.txt` and uses the final exact 40-character provider commit;
- verify no Product Docker image installs `requirements-dev.txt` or the `project-standards` distribution.

WCC additionally runs targeted backend tests for retained route-registry and API/ORM name behavior. Its broad shared-component rules are not approximated by JSX source scans. No UI source or user-visible behavior change is planned, so the WCC live UI handoff sequence is not triggered solely by instruction, test ownership, or dependency-file migration. If implementation changes `ui/**` or user-visible behavior, the exact WCC UI sequence becomes mandatory.

MTP additionally runs its interactive prompt behavior tests and the full approved production-plan verification only if Product runtime behavior changes. This task plans no production-bound runtime behavior or external write.

### Provider And Plugin Validation

- Validate the `project-standards` plugin.
- Validate the changed `agent-plugins` marketplace plugin and quick-validate its changed Ozon skill.
- Quick-validate every `project-standards` skill and every changed skill in another affected plugin.
- Run the complete provider pytest suite.
- Install the final cache-busted local revisions of both changed plugins and verify the exposed skills read the changed canonical contracts.
- Run `project-standardize --workspace-root <explicit-workspace-root> --check` after consumer migration.
- Run complete project-standard semantic acceptance for WCC and MTP after mechanical checks pass; use the structured `project-standard-audit` report only when explicitly requested.

### Publication Acceptance

- Commit and push each changed provider repository before its consumer gitlink or exact dependency pin.
- Commit and push `project-standards`, `agent-plugins`, WCC, and MTP in logical repository-local commits.
- Verify every changed repository is clean and its final branch head equals `origin/main`.
- Verify WCC and MTP gitlinks equal the published provider revisions.
- Verify both consumer development requirement pins equal the final published `project-standards` commit.
- Do not commit any `.spec` file.

## Terminal Completion Audit

After implementation appears complete, audit the whole current task again from scratch against this specification, its paired goal, and every referenced stable owner. Inspect all eight affected tracked repositories, both consumer dependency/runtime boundaries, the installed provider distribution and plugins, all migrated rule/test/checker families, every retained consumer exception, submodule gitlinks, exact dependency pins, and remote publication state.

If the audit finds any unmapped source test/helper, duplicated checker, missing branch, stale reference, stale discovery path, runtime dependency regression, unpinned provider, unpushed commit, dirty worktree, or unverified requirement, keep the goal active, fix every finding, rerun affected verification, and start a new full audit from scratch. Repeat until one full audit performed after the last fix finds no unfinished requirement. Do not create a separate completion ledger or evidence artifact.
