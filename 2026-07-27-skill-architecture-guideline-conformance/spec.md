# Skill Architecture Guideline Conformance

## Outcome

Bring the current plugin and skill architecture into semantic and mechanical conformance with the current OpenAI skill and plugin guidance for GPT-5.6 Sol without collapsing approved provider boundaries or deleting any of the 42 current skill identities.

The final state has one coherent subagent transport model, reliable skill activation and output contracts, concise front-loaded discovery metadata, conditional reference loading, one owner for shared audit orchestration, exact deterministic validation for machine-facing skill metadata, and no approved obsolete tracked artifacts.

## Coordinating And Source Owners

This is a dedicated multi-repository implementation specification coordinated by `agent-plugins`.

- `agent-plugins/DESIGN.md` owns the stable provider and plugin architecture.
- `agent-plugins/plugins/agent-workflows/lib/subagent-transport/protocol.md` owns direct-agent and registry-backed pool transport semantics.
- `agent-plugins/plugins/agent-workflows/lib/section-audit/protocol.md` owns shared section-audit orchestration.
- `agent-plugins/plugins/agent-workflows/lib/sequential-batch/protocol.md` owns registry-backed sequential-batch orchestration.
- Each `agent-workflows` `SKILL.md` owns only its workflow-specific scope, inputs, semantic requirements, and handoff.
- `project-standards/plugins/project-standards/skills/project-instruction-developer/references/skill-model.md` owns reusable skill metadata, invocation, behavioral-evaluation, and plugin-support-owner rules.
- `project-standards/plugins/project-standards/skills/project-instruction-developer` owns exact deterministic validation for its mechanically decidable skill-metadata subset.
- `project-standards/plugins/project-standards/skills/sqlalchemy-developer` owns the reusable SQLAlchemy contracts and their conditional reference routing.
- Each other affected `project-standards` skill remains the sole owner of its existing reusable standard.
- `marketplace-tr-priority/.agents/skills/**` remains the owner of its four approved project-local skills and owner-local tools.
- Current official OpenAI skill and plugin guidance defines the external format and discovery constraints; project standards retain opinionated ownership and verification rules that do not contradict it.

## Verified Baseline

- Four installable plugins and 42 current skills pass their existing structural validators.
- All 42 current skills are covered and activated by the combined current and temporary audit corpus.
- The fresh 34-case GPT-5.6 Sol run produced 32 passes:
  - one real instruction-classification case showed unstable `project-standard-audit` activation or omitted the mandatory approved-ledger boundary;
  - one temporary workflow-input case incorrectly expected `workflow-container-developer` for input-data preparation and is a test-definition defect.
- Direct/pool transport rules conflict when one registry-backed workflow currently has one active worker.
- Seven references longer than 100 lines lack a top table of contents, and `python-developer` does not directly expose its conditional anti-pattern-card reference.
- The 42 qualified names, descriptions, and absolute skill paths occupy approximately 12,100 raw discovery characters before formatting and before unrelated installed skills.
- `ozon-attribute-map/agents/openai.yaml` does not mention `$ozon-attribute-map` in `interface.default_prompt`.
- `zitadel-developer/agents/openai.yaml` contains only generic optional interface metadata.
- The deleted `project-standardize` identity remains in one forbidden behavioral-evaluation set.
- `trendyol-size-chart-parse/tool/size_chart_image_content_count.py` has no current skill, design, test, or code caller.
- `trendyol-size-chart-parse/scratch/images/**` contains ignored user-local files whose mutation was not authorized.

## Approved Decisions

### Provider And Skill Identity

- Retain the four plugin boundaries `agent-workflows`, `project-standards`, `workflow-container-agent-tools`, and `marketplace-agent-tools`.
- Retain all 42 current skill identities, including the four project-local `marketplace-tr-priority` skills.
- Keep workflow input preparation separate from workflow-container code development.
- Keep explain modes and audit types as independently triggerable skills.
- Do not restore `project-standardize`; its functionality remains consolidated under `project-standard-audit`.

### Transport Semantics

- `direct_agent` means one independently tracked current subagent whose identifier is held in parent runtime state and for which `agent.json` is forbidden.
- `agent_pool` means a registry-backed workflow that owns one run-local `agent.json`; it may currently contain one or more active agents.
- A locally executed role with no subagent has no subagent transport mode.
- Section-audit and sequential-batch workflows use `agent_pool` because their assignment registry is part of their workflow contract even when current capacity is one.
- One delegated code-antipattern semantic role uses `direct_agent`.
- Canonical plugin contracts remain harness-neutral. They describe absence of inherited surrounding conversation context; a Codex adapter uses the current harness field rather than exposing an obsolete `fork_context` field in the canonical contract.

### Activation And Behavioral Evaluation

- `project-standard-audit` front-loads classification and ownership-audit triggers in its description.
- A classification that proposes moving instruction or standard ownership must state that no move is allowed before an explicit approved source-to-target ledger.
- The versioned provider corpora cover every current skill through direct, indirect, incomplete, negative, and overlap scenarios.
- The workflow-input scenario expects only the input-creation and generally applicable foundation owners; it does not require the code-development skill.
- Deleted skill identities are forbidden as expected or forbidden corpus entries because such entries do not exercise current discovery behavior.
- Behavioral evaluation remains a model phase separate from deterministic validation, pytest, and semantic audit.

### Metadata And Discovery

- An `agents/openai.yaml` `interface.default_prompt`, when present, contains the exact `$<skill-name>` invocation.
- An `interface.short_description`, when present, remains within the current official length range.
- The exact machine-facing subset above is validated by one provider-owned deterministic checker with success, malformed-input, missing-invocation, and boundary-length behavior coverage.
- Optional `agents/openai.yaml` files remain only when they carry meaningful UI metadata, tool dependencies, or invocation policy. The generic ZITADEL file is deleted.
- Every skill description remains concise, directly names its capability and trigger boundary, and front-loads discriminating terms.
- Description changes are applied by semantic family rather than a mechanical mass rewrite. The combined description length is reduced from 6,284 characters to at most 5,000 while preserving all current activation boundaries.

### Progressive Disclosure

- Every retained reference longer than 100 lines has a top table of contents.
- Every skill-local reference is linked directly from its owning `SKILL.md` with an explicit condition for reading it.
- `python-developer` links `code-antipattern-cards.md` only for anti-pattern semantic audits.
- `project-instruction-developer` always loads its core instruction protocol for instruction changes and conditionally loads repository-reference and skill-model contracts only when those boundaries are affected.
- The former monolithic SQLAlchemy contract is replaced by directly linked conditional references for:
  - ORM ownership and mapped-field behavior;
  - session and transaction behavior, including production-write boundaries;
  - schema migrations;
  - table lifecycle profiles;
  - test-database seeding.
- No SQLAlchemy requirement is dropped, weakened, duplicated, or left under the removed monolithic path.

### Shared Audit Orchestration

- `lib/section-audit/protocol.md` is the single owner of generic registry creation, section-agent creation, validation, correction, coverage comparison, deterministic merge, final validation, and transport recovery.
- `code-audit` and `instruction-audit` retain only their distinct owner closure, scope derivation, requirement inventory, evidence semantics, output identity, and handoff.
- `code-antipattern-audit` remains a separate two-perspective workflow.
- Redundant restatements in `script-workflow-owner.md` are removed without changing its applicability or implementation contract.

### Cleanup

- Remove the stale `project-standardize` corpus entry.
- Remove `marketplace-tr-priority/.agents/skills/trendyol-size-chart-parse/tool/size_chart_image_content_count.py`.
- Preserve every ignored file under `trendyol-size-chart-parse/scratch/images/**`. Preserve the tracked scratch ignore boundary while those user-local files remain; deleting or moving them requires a separate explicit user request.
- Remove empty tracked or generated owner paths only when they become empty after authorized changes. Ignored caches are not source artifacts and are not included in commits.

## Source-To-Target Ledger

| Source | Approved target |
| --- | --- |
| `agent-workflows/lib/subagent-transport/protocol.md` count-based pool restriction | Registry-based `direct_agent` and `agent_pool` definitions in the same canonical owner |
| `agent-workflows/lib/section-audit/protocol.md` unconditional pool plus obsolete context field | Registry-backed pool contract plus harness-neutral no-inherited-context wording in the same owner |
| `agent-workflows/lib/sequential-batch/protocol.md` and `sequential-batch/SKILL.md` conflicting one-worker behavior | One registry-backed pool contract owned by the protocol and referenced by the skill |
| `code-antipattern-audit/SKILL.md` single delegated role recorded as pool | `direct_agent` with parent-owned identifier and no registry |
| Generic section orchestration repeated by `code-audit/SKILL.md` and `instruction-audit/SKILL.md` | `agent-workflows/lib/section-audit/protocol.md`; skills retain only distinct semantics |
| Narrow `project-standard-audit` discovery description | Front-loaded classification and ownership-audit trigger in the same skill |
| Instruction-classification output boundary implied only by migration prose | One explicit classification-to-approved-ledger prerequisite in `project-instruction-developer` ownership |
| Temporary supplemental behavioral scenarios | Appropriate versioned provider or project corpus by the skills under test |
| Stale `project-standardize` forbidden entry | Removal; no replacement identity |
| Invalid workflow-input expected activation | Correct expected set in the owning provider corpus |
| Official `openai.yaml` prompt and short-description exact subset without provider checker | `project-instruction-developer` normative machine-facing metadata contract, checker manifest, checker implementation, and owner-local behavior tests |
| `ozon-attribute-map/agents/openai.yaml` prompt without explicit invocation | Same project-local metadata file with `$ozon-attribute-map` |
| Generic `zitadel-developer/agents/openai.yaml` | Deletion |
| Long references without top navigation | Top table of contents in each retained same owner |
| `python-developer/references/code-antipattern-cards.md` exposed only through another provider | Conditional direct link from `python-developer/SKILL.md` |
| Unconditional project-instruction reference loading | Core-plus-conditional routing in `project-instruction-developer/SKILL.md` |
| `sqlalchemy-developer/references/sqlalchemy.md` | Conditional owner-local references for ORM, session/transaction, migration, lifecycle, and test-database concerns; old path deleted |
| Redundant closing ownership/applicability bullets in `script-workflow-owner.md` | Deletion with semantics retained by its heading and existing sections |
| Long skill descriptions across the 42-skill catalog | Semantically shortened same-skill descriptions, family-by-family, with versioned behavior evaluation |
| Unreferenced `size_chart_image_content_count.py` | Deletion |
| Ignored `scratch/images/**` user data and its active ignore file | Retained unchanged pending explicit user direction |

## Compatibility And Migration

- There are no compatibility aliases, forwarding documents, duplicate old reference paths, or transition-only wrappers.
- Every direct consumer of a renamed or split reference moves in the same change.
- Plugin source and installed cache are updated to the same final revision before fresh-session acceptance.
- Consumer `Required Standards` catalogs remain unchanged because no skill identity is added or removed.
- The current conversation may retain a stale discovery snapshot; final activation acceptance must use a fresh Codex session after plugin installation.

## Verification And Acceptance

Apply each repository's current `AGENTS.md` verification contract.

Executable behavior verification must cover:

- direct-agent and registry-backed pool success paths;
- one-worker registry-backed pool behavior;
- no-registry direct-agent behavior;
- metadata checker clean metadata, missing `$skill-name`, short-description boundaries, malformed metadata, and both provider and project-local skill roots;
- packaged checker discovery and execution through `project-standard-check`;
- every changed owner-local tool and test family.

Instruction and reference verification must include:

- official plugin validation for all four plugin roots;
- official quick validation for all 42 current skill roots;
- provider and project pytest suites;
- deterministic listing of every versioned behavioral corpus;
- a fresh GPT-5.6 Sol run over the combined versioned corpora with every case passing;
- proof that every current skill appears in at least one expected activation set and is activated in the fresh run;
- exact absence of stale current-provider skill identities in expected and forbidden sets;
- current provider source and installed cache equality;
- full mechanical checks for all governed repositories;
- an independent semantic audit of the complete current plugin, skill, reference, ownership, and consumer-binding scope.

Description compression is accepted only when the full behavioral corpus remains clean. A failed case returns to the owning description or contract; weakening expected activation or semantic invariants to manufacture a pass is forbidden.

After presumed completion, run `Terminal Completion Audit` from the paired goal against this specification and every source owner. Any finding restarts the audit-fix cycle from complete owner discovery. Completion requires one fresh zero-finding audit after the last fix, all required verification still passing, clean task-owned worktrees, logical commits, pushed remotes, and upstream equality.

