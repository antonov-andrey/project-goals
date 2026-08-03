# Project Goals

`project-goals` является единственным tracked coordination repository для cross-repository implementation goals. Он хранит task contracts и checkpoint identity, но не владеет Product code, deployment implementation либо reusable workflow logic.

## Task Artifact Model

Один exact common prefix создаёт один root directory:

```text
<common-prefix>/
  checkpoint.yaml
  goal.md
  spec.md
```

Common prefix является basename, содержит creation date и stable semantic name и совпадает с task branch и `.worktree/<common-prefix>` во всех participating implementation repositories. Repository `project-goals` не создаёт task branch, linked worktree либо bootstrap manifest. Новые project-local `.spec/`, task-artifact symlinks и physical copies запрещены. Уже существующие ignored pre-cutover task files остаются inert до отдельного explicit deletion request и не являются input нового lifecycle.

`spec.md` владеет task-specific implementation contract. `goal.md` владеет concise executable objective и ссылками на `spec.md` и stable source owners. Оба файла готовит `agent-workflows:goal-brainstorm`. `checkpoint.yaml` владеет только published cross-repository closing commit sets, accepted checkpoint identity и current task-resource state; он не копирует requirements или completion evidence.

До activation `goal-brainstorm` может пересматривать тот же task directory непосредственно в canonical checkout ветки `main`. Каждое согласованное изменение task artifacts использует `Coordination Main Transaction` из `agent-plugins/plugins/agent-workflows/skills/goal-brainstorm/references/worktree-contract.md`. Этот owner commit-ит и push-ит только exact task-directory delta, безопасно повторяет disjoint concurrent update и отвергает overlapping change. Никакое согласованное изменение не остаётся только в working tree, отдельной branch либо временном coordination worktree.

`seal` проверяет exact published `main` commit и fingerprints полного candidate, но означает только internally consistent review candidate. User approval возникает только при отдельной persistent-goal activation. До activation `revise` сохраняет common prefix и published history, после чего новые согласованные изменения снова публикуются direct-main transaction. После activation `spec.md` и `goal.md` immutable; implementation изменяет только `checkpoint.yaml` через его lifecycle owners.

## Checkpoint Contract

`checkpoint.yaml` использует closed schema:

```yaml
schema_version: 2
accepted_checkpoint_id: ""
task_resource_state: retained
checkpoint_list:
  - checkpoint_id: checkpoint-0001
    project_list:
      - project_path: agent-plugins
        git_commit_final: 0123456789abcdef0123456789abcdef01234567
```

Empty `accepted_checkpoint_id` означает, что ни один checkpoint ещё не прошёл merge acceptance; `null` для этого не используется. `checkpoint_id` monotonically increases inside one task directory. Existing checkpoint entries immutable and `checkpoint_list` append-only. Обычно `accepted_checkpoint_id` переходит на next checkpoint после полной acceptance. После failed acceptance он может перейти сразу на later fix-forward checkpoint только если для каждого project все intervening checkpoint commits являются ancestors-or-equal его full snapshot commit; поэтому skipped identifiers не означают пропущенный code state.

`task_resource_state` использует только `retained` либо `deleted`. `retained` означает, что private lifecycle state и task-owned external/Git resources ещё могут существовать. Только успешный `goal-delete` переводит поле в `deleted` после idempotent cleanup; обратный переход запрещён. Состояние `deleted` закрывает новые checkpoint, merge и task mutation, но сохраняет `spec.md`, `goal.md` и полный checkpoint history в permanent registry.

Каждый checkpoint является полным snapshot всех participating implementation repositories, включая repositories без нового commit после предыдущего checkpoint. Workspace root является physical parent canonical checkout `project-goals`; `project_path` разрешается только относительно этого exact root. `project_list` сортируется по `project_path`. Каждая entry содержит ровно этот workspace-relative `project_path` и full `git_commit_final` из соответствующего repository. Absolute paths, empty/`.`/`..` components, symlink escape, duplicate projects, abbreviated commits и missing origin objects запрещены. Coordination repository `project-goals` не входит в self-referential `project_list`.

Один direct-main commit в `project-goals/main` атомарно публикует весь checkpoint set. Он не делает multi-repository merge атомарным.

## Checkpoint Publication

`agent-workflows:goal-checkpoint` создаёт checkpoint для active goal либо неразрушающего fix-forward внутри уже запущенного `goal-merge`; отдельное согласование такого checkpoint не требуется. Skill получает logical closing commits через `agent-workflows:git-commit`, проверяет clean task roots, exact pushed task refs и full origin commit identities, а также доказывает, что каждый `git_commit_final` является descendant-or-equal current `origin/main` соответствующего repository и prior checkpoint commit той же task. После первого published checkpoint task refs append-only: rebase, reset, history rewrite и force-push запрещены, поэтому каждый recorded object остаётся достижимым. Если параллельная task уже продвинула main, implementation branch явно интегрирует новый main новым descendant commit, повторяет affected verification и получает новый closing commit; checkpoint workflow не rebase-ит и не merge-ит Product source скрыто. После proof skill append-ит один complete snapshot через общую direct-main transaction. Concurrent unrelated update перечитывается и применяется повторно, а изменение того же task directory блокирует publication до разрешения конфликта.

## Merge And Acceptance

`agent-workflows:goal-merge` работает в отдельном эксклюзивном Codex thread и обрабатывает ровно один selected checkpoint: ordinary next checkpoint либо доказанный full fix-forward successor failed checkpoint. До чтения либо изменения merge state он получает non-blocking OS-level exclusive lock в Git common directory canonical `project-goals` checkout. Первый успешно проверенный merge создаёт durable workspace merge-owner marker, который сохраняет exclusive task ownership через внешнюю primary acceptance и удаляется только после accepted-pointer publication; второй local task завершается без mutation. OS lock сериализует каждый короткий state transition, а durable owner закрывает промежуток между отдельными `merge` и `accept` invocations без ложного утверждения, что один process удерживает kernel lock во время внешней acceptance. До первого project mutation workflow preflight-ит current `accepted_checkpoint_id`, exact origin objects, task branch identity, every project main ancestry и отсутствие unrelated local state. Каждый project main update выполняется fast-forward compare-and-swap к exact `git_commit_final`. Predictable non-fast-forward divergence блокирует весь selected checkpoint до нового closing snapshot; concurrent remote change после preflight не перезаписывается и становится resumable partial-merge failure. Current manually started workflow поддерживает одного merge operator workspace; remote multi-host lock service не вводится скрыто.

Multi-repository merge возобновляемый, но не атомарный. После interruption skill сравнивает каждый project main с target commit и продолжает только недостающие merges. Automatic revert и history rewrite запрещены. После всех merges skill разворачивает exact main commits в permanent primary development environment и выполняет полный applicable acceptance. При failure accepted pointer не меняется и durable merge journal сохраняет failed target. Входящая в approved goal неразрушающая ошибка исправляется только в recorded task worktrees, проходит required verification и получает новый full descendant fix-forward checkpoint через обычные implementation, `git-commit` и `goal-checkpoint` owners без дополнительного разрешения. Тот же exclusive workflow может выбрать этот checkpoint вместо failed target только после per-project ancestry proof, что новый snapshot включает каждый intervening commit; одна merge operation всё равно merge-ит и принимает ровно один selected checkpoint. Workflow запрашивает отдельное решение только для новых полномочий либо неизбежной потери данных, когда lossless migration невозможна.

Только после зелёной acceptance skill атомарно обновляет `accepted_checkpoint_id` и публикует `checkpoint.yaml` в `project-goals/main`.

## Lifecycle Serialization

Каждый mutating lifecycle skill получает non-blocking task-specific OS lock по exact common prefix в Git common directory canonical `project-goals` checkout. Любая mutation checkout либо ref `project-goals/main` дополнительно получает один краткоживущий workspace-global coordination write lock, поэтому shared working tree никогда не изменяется параллельно. `goal-merge` удерживает отдельный workspace-global merge lock во время one-checkpoint merge и получает coordination write lock только для коротких direct-main transactions. `goal-brainstorm` seal/revise, `goal-checkpoint`, `goal-merge` и `goal-delete` не могут менять один task одновременно. Lock files являются private runtime state и не коммитятся; process exit освобождает kernel lock, а durable operation journal определяет, нужно ли следующему invocation resume-ить незавершённую transaction.

## Task Deletion

Goal completion не удаляет resources или artifacts. `agent-workflows:goal-delete` запускается только по явному запросу пользователя удалить exact common prefix. Этот запрос является authority для всего recorded task scope: dirty, unmerged, changed и частично отсутствующие task worktrees/refs не требуют дополнительного разрешения. Уже отсутствующий in-scope resource считается успешно очищенным.

Skill выполняет current project-owned external cleanup hooks и registry-state mutation из clean temporary `origin/main` checkouts, затем удаляет все recorded task worktrees, remote refs, local refs, legacy bootstrap carriers и private lifecycle state. Shared provider excludes не принадлежат одной goal; удаляется только exact legacy bootstrap exclude вместе с его marker. Skill не требует latest accepted checkpoint, pristine task commit, clean implementation либо `project-goals` main или сохранения исторической branch identity; unrelated canonical changes сохраняются, а safe fast-forward остаётся best-effort. Блокирующая проверка допустима только если exact scope/ownership нельзя установить, можно затронуть primary/shared/foreign state, concurrent recreation мешает завершению либо отсутствие известного ресурса нельзя наблюдать.

Каталог `<common-prefix>/` никогда не удаляется: после успешной очистки `checkpoint.yaml` получает `task_resource_state: deleted` и весь goal остаётся в едином tracked registry. Raw `rm` вне exact private task namespace, автоматическая stale-task эвристика и deletion из-за одного goal status запрещены. Interrupted cleanup возобновляется по durable private journal.

## Serialization

Все project-owned machine-readable files следуют `project-standards:project-foundation`, reference `Machine-Readable Format Contract`. Этот repository использует `.yaml`; `.yml` запрещён. JSON либо TOML допустимы только для будущего owner, чей внешний ecosystem или tool делает их каноническими.
