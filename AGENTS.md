# Repository Guidelines

## Table Of Contents

- [Required Standards](#required-standards)
- [Project Contract](#project-contract)
- [Key Directory Map](#key-directory-map)

## Required Standards

- `project-standards:aws-cloudformation-developer`
- `project-standards:docker-compose-developer`
- `project-standards:http-api-client-developer`
- `project-standards:kubernetes-developer`
- `project-standards:legacy-python-maintainer`
- `project-standards:project-documentation-developer`
- `project-standards:project-foundation`
- `project-standards:project-instruction-developer`
- `project-standards:project-standard-audit`
- `project-standards:pytest-developer`
- `project-standards:python-cli-developer`
- `project-standards:python-developer`
- `project-standards:python-logging-developer`
- `project-standards:python-retry-developer`
- `project-standards:react-ui-developer`
- `project-standards:rest-api-server-developer`
- `project-standards:runtime-config-developer`
- `project-standards:sqlalchemy-developer`
- `project-standards:submodule-developer`
- `project-standards:typescript-developer`
- `project-standards:zitadel-developer`

## Project Contract

- This repository is the canonical tracked coordination owner for cross-repository implementation goals.
- This repository contains no Product runtime, deployment implementation, reusable workflow implementation, or project source checkout.
- This repository uses only its canonical `main` checkout. Task branches, linked worktrees, and bootstrap manifests are forbidden.
- `agent-workflows:goal-brainstorm` owns specification and goal preparation.
- `agent-workflows:goal-checkpoint` owns checkpoint publication.
- `agent-workflows:goal-merge` owns one-checkpoint-at-a-time merge and primary-environment acceptance.
- `agent-workflows:goal-delete` owns explicitly authorized idempotent task-resource cleanup and the retained registry-state transition.
- Every lifecycle mutation MUST commit and push its exact coordination delta directly to `main` through the serialized transaction owned by the applicable lifecycle skill.
- Direct edits that bypass the applicable lifecycle skill are forbidden.

## Key Directory Map

```text
project/
  AGENTS.md
  <common-prefix>/
    checkpoint.yaml
    goal.md
    spec.md
  DESIGN.md
  README.md
```

- `AGENTS.md`: repository-root canonical instruction owner.
- `<common-prefix>/`: one permanent tracked goal-registry directory identified by the exact task common prefix; cleanup never deletes it.
- `<common-prefix>/checkpoint.yaml`: canonical checkpoint list, accepted-checkpoint pointer, and retained/deleted task-resource state for one task.
- `<common-prefix>/goal.md`: concise executable objective prepared by `agent-workflows:goal-brainstorm`.
- `<common-prefix>/spec.md`: task-specific implementation contract prepared by `agent-workflows:goal-brainstorm`.
- `DESIGN.md`: canonical task-artifact, checkpoint, merge, and deletion design owner.
- `README.md`: concise user-facing repository purpose and layout.
