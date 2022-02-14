#github #pipelines

**Zdarzenie** to określona aktywność w repozytorium, która uruchamia [Workflow](Workflow.md).

##### Spis treści

- [Obsługa zdarzeń w workflow](#Obsługa%20zdarzeń%20w%20workflow)
  - [Typy](#Typy)
  - [Filtry](#Filtry)
- [Lista zdarzeń](#Lista%20zdarzeń)

# Obsługa zdarzeń w workflow

[Workflow](Workflow.md) określa, które Eventy mogą powodować jego uruchomienie.

Można zdefiniować:

- pojedyncze **zdarzenie**: `  on: push `,
- jeden z wielu **zdarzeń**: `on: [push, fork]` (każdy **zdarzenie** uruchamia nowy [Workflow](Workflow.md)),
- harmonogram: `on.schedule`,
- wywołanie przez inny [Workflow](Workflow.md):  `on.workflow_call`,
- wywołanie manualne: [`on.workflow_dispatch`.

Więcej: [**Workflow syntax** > `on`](Workflow%20syntax.md#on).

## Typy

Niektóre **zdarzenia** posiadają wiele typów aktywności. Dla tych **zdarzeń** można określić, które typy aktywności wyzwolą uruchomienie [Workflow](Workflow.md).

Więcej: [`on.<event_name>.types`](Workflow%20syntax.md#on%20event_name%20types).

## Filtry

Niektóre **zdarzenia** posiadają filtry, które dają większą kontrolę nad tym, kiedy [Workflow](Workflow.md) powinien zostać uruchomiony.

Więcej: [`on.<event_name>.<filter_name>`](Workflow%20syntax.md#on%20event_name%20filter_name).

# Lista zdarzeń

- [`branch_protection_rule`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#branch_protection_rule)
- [`check_run`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#check_run)
- [`check_suite`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#check_suite)
- [`create`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#create)
- [`delete`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#delete)
- [`deployment`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#deployment)
- [`deployment_status`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#deployment_status)
- [`discussion`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#discussion)
- [`discussion_comment`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#discussion_comment)
- [`fork`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#fork)
- [`gollum`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#gollum)
- [`issue_comment`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#issue_comment)
- [`issues`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#issues)
- [`label`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#label)
- [`milestone`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#milestone)
- [`page_build`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#page_build)
- [`project`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#project)
- [`project_card`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#project_card)
- [`project_column`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#project_column)
- [`public`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#public)
- [`pull_request`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#pull_request)
- [`pull_request_comment` (use `issue_comment`)](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#pull_request_comment-use-issue_comment)
- [`pull_request_review`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#pull_request_review)
- [`pull_request_review_comment`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#pull_request_review_comment)
- [`pull_request_target`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#pull_request_target)
- [`push`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#push)
- [`registry_package`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#registry_package)
- [`release`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#release)
- [`repository_dispatch`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#repository_dispatch)
- [`schedule`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#schedule)
- [`status`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#status)
- [`watch`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#watch)
- [`workflow_call`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_call)
- [`workflow_dispatch`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_dispatch)
- [`workflow_run`](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_run)
