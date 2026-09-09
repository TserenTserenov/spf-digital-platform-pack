---
id: DP.M.426
type: method
title: guard-scope-by-ref-namespace-not-command-form — guard разграничивает по целевому namespace, не по форме команды
kind: Method
pack: PACK-digital-platform
domain: digital-platform / git-security
trust: observed
epistemic_stage: confirmed
domains: [git-hooks, guard-scope, force-push, ref-namespace]
source_session: "git commit 9f669de28c (DS-my-strategy, WP-530 Ф25)"
valid_from: 2026-09-09
schema_version: 1
related:
  see_also: []
---

# DP.M.426 — Guard, блокирующий по форме операции, сузить до защищаемого namespace

## Определение

Guard против опасной операции (force-push, force delete, rebase) написанный против ФОРМЫ команды блокирует все её применения одинаково — включая легитимные операции другого назначения, использующие ту же форму на другом ref-namespace того же репозитория. Фикс — сузить guard до пространства имён ref, которое он обязан защищать, а не до формы команды.

## Живой случай

Guard против force-push был написан против формы (`git push --force`), но межмашинная аренда замка тоже перезаписывает git-notes через force-push-совместимый push — единственный способ атомарно передать заметку новому владельцу. Guard блокировал легитимную перезапись git-notes (вне `refs/heads/*` и `refs/tags/*`) как если бы это было опасное переписывание истории ветки.

## Когда применять

- Один и тот же git-примитив (force-push, force delete, rebase) используется в репозитории для двух операций разного назначения на разных ref-неймспейсах.
- Guard, распознающий только форму команды, даёт систематический false positive на легитимном юзкейсе соседнего namespace.

## Структура

Guard проверяет целевой ref/namespace операции (`refs/heads/*`, `refs/tags/*` vs остальное), не только использованный флаг команды — блокирует только операции внутри защищаемого namespace.

## Применимо к

Любой git-hook/guard, защищающий от опасной формы операции, в репозитории, где та же форма легитимно используется для другого назначения на другом ref-namespace.

## Связи
- Источник: WP-530 Ф25
