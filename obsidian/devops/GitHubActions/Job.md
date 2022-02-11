#github #pipelines

**Zadanie** (**job**) to zbiór **kroków** dla [Workflow](Workflow.md), które wykonywane są przez tego samego wykonawcę.
Każdy **krok** jest:

- albo skryptem powłoki, który zostanie wykonany,
- albo [Action](Action.md), która zostanie uruchomiona.

Kroki wykonywane są w kolejności na tym samym [Runnerze](Runner.md), współdzieląc dane.

Można skonfigurować zależności **zadania** z innymi **zadaniami**. Domyślnie **zadania** nie mają żadnych zależności i działają równolegle do siebie. Gdy zadanie ma zależność od innego zadania, będzie ono czekać na zakończenie zadania zależnego zanim będzie mogło zostać uruchomione.

# Jobs: w pliku workflow

[źródło](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)

W ramach pojedynczego [Workflow](Workflow.md) można uruchomić nieograniczoną liczbę jobów, o ile nie przekroczy się limitów użycia [Workflow](Workflow.md).

Składnia i atrybuty Jobów opisane są w rodziale [**Workflow syntax > jobs**](Workflow%20syntax.md#jobs).