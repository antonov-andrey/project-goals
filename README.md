# Project Goals

Этот репозиторий хранит tracked specification, goal и checkpoint metadata для cross-repository задач. Один task common prefix соответствует одному root directory с файлами `spec.md`, `goal.md` и `checkpoint.yaml`.

Repository использует только canonical checkout ветки `main`: task branches, linked worktrees и bootstrap manifests здесь не создаются. Task lifecycle выполняют skills `agent-workflows:goal-brainstorm`, `agent-workflows:goal-checkpoint`, `agent-workflows:goal-merge` и `agent-workflows:goal-delete`; каждый из них commit-ит и push-ит свой exact coordination delta напрямую в `main`. Ручное изменение task artifacts вне соответствующего workflow не поддерживается.
