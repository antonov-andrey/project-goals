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
- `agent-workflows:goal-brainstorm`
- `linear-agent-tools:task-graph-create`

## Project Contract

- This repository is the canonical tracked source owner for implementation goals before Linear handoff.
- One current goal directory contains only `goal.md` and `spec.md`.
- `agent-workflows:goal-brainstorm` owns source authoring, revision, semantic review, commit, and push before Linear handoff.
- `linear-agent-tools:task-graph-create` reads one exact Git commit and publishes the operational graph to Linear without mutating this repository.
- After successful Linear handoff, all task, dependency, review, acceptance, branch, pull-request, merge, and rework state belongs to Linear, Git, and GitHub.
- This repository contains no task graph, Product runtime, deployment implementation, reusable workflow implementation, project source checkout, task branch, linked worktree, bootstrap manifest, or project-local `.spec` copy.
- Existing `checkpoint.yaml` files are retained historical records from the superseded pre-Linear lifecycle. New workflows MUST NOT create or mutate them.
- Direct edits that bypass `agent-workflows:goal-brainstorm` are forbidden for current `goal.md` and `spec.md` authoring.

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
- `<common-prefix>/`: one tracked source directory identified by its exact common prefix.
- `<common-prefix>/checkpoint.yaml`: optional retained historical artifact from the superseded pre-Linear lifecycle; current workflows do not create or mutate it.
- `<common-prefix>/goal.md`: concise source outcome prepared by `agent-workflows:goal-brainstorm`.
- `<common-prefix>/spec.md`: complete source implementation contract prepared by `agent-workflows:goal-brainstorm`.
- `DESIGN.md`: canonical source-artifact and Linear-handoff design owner.
- `README.md`: concise user-facing repository purpose and layout.
