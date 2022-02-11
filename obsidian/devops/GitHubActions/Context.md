#github #pipelines

[źródło](https://docs.github.com/en/actions/learn-github-actions/contexts#inputs-context)

Konteksty są sposobem dostępu do informacji o przebiegach [Workflowó](Workflow.md), [Runnerów](Runner.md), [Jobów](Job.md) i **krokach**. Każdy kontekst jest obiektem zawierającym właściwości, które mogą być stringami lub innymi obiektami.

Konteksty, obiekty i właściwości różnią się znacząco w zależności od warunków uruchomienia [Workflow](Workflow.md). 
Np. kontekst [`matrix`](https://docs.github.com/en/actions/learn-github-actions/contexts#matrix-context) jest wypełniany tylko dla [Jobów](Job.md) w macierzy budowy.

##### Spis treści

- [Konteksty w workflow](#Konteksty%20w%20workflow)
- [Dostępność kontekstów](#Dostępność%20kontekstów)

# Konteksty w workflow

W GitHub Actions można odwołać się do następujących kontekstów:

| Nazwa                                                                                           | Opis                                                                                                                        |
| ----------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| [`github`](https://docs.github.com/en/actions/learn-github-actions/contexts#github-context)     | Informacje o przebiegu [Workflow](Workflow.md).                                                                             |
| [`env`](https://docs.github.com/en/actions/learn-github-actions/contexts#env-context)           | Zmienne środowiskowe ustawione dla [Workflow](Workflow.md)/[Job](Job.md)/**kroku**.                                         |
| [`job`](https://docs.github.com/en/actions/learn-github-actions/contexts#job-context)           | Informacje o obecnym [Jobie](Job.md).                                                                                       |
| [`steps`](https://docs.github.com/en/actions/learn-github-actions/contexts#steps-context)       | Informacje o obecnym **kroku**.                                                                                             |
| [`runner`](https://docs.github.com/en/actions/learn-github-actions/contexts#runner-context)     | Informacje o [Runnerze](Runner.md) uruchomionym dla obecnego [Joba](Job.md).                                                |
| [`secrets`](https://docs.github.com/en/actions/learn-github-actions/contexts#secrets-context)   | Nazwy i wartośći sekretów, które są dostępne dla obecnego [Workflow](Workflow.md).                                          |
| [`strategy`](https://docs.github.com/en/actions/learn-github-actions/contexts#strategy-context) | Informacje o strategii matrycy uruchomienia ([`jobs.<job_id>.strategy`](Workflow%20syntax.md#jobs%20job_id%20strategy)).    |
| [`matrix`](https://docs.github.com/en/actions/learn-github-actions/contexts#matrix-context)     | Własności matrycy uruchomienia ([`jobs.<job_id>.strategy.matrix`](Workflow%20syntax.md#jobs%20job_id%20strategy%20matrix)). |
| [`needs`](https://docs.github.com/en/actions/learn-github-actions/contexts#needs-context)       | Dane wyjściowe wszystkich [Jobów](Job.md), które są zdefiniowane jako zależność obecjego [Joba](Job.md).                    |
| [`inputs`](https://docs.github.com/en/actions/learn-github-actions/contexts#inputs-context)     | Dane wejściowe reużywalnego [Workflow](Workflow.md).                                                                        |

# Dostępność kontekstów

Nie wszystkie konteksty są dostępne w każdym miejscu [Workflow](Workflow.md). Kiedy dany kontekst jest dostępny, zawarte jest w tabeli: [Context availability](https://docs.github.com/en/actions/learn-github-actions/contexts#context-availability).

