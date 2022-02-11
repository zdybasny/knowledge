#github #pipelines

**Przepływ pracy** (**workflow**) jest konfigurowalnym, zautomatyzowanym procesem, który uruchamia jeden lub więcej [Jobów](Job.md). **Workflow** jest definiowany przez plik YAML, który jest dołączony do repozytorium.

Jest uruchamiany, gdy zostanie wowołany:

- przez [Event](Event.md) na repozytorium,
- ręcznie: `on.workflow_dispatch`,
- lub według określonego harmonogramu: `on.schedule`.

Twoje repozytorium może posiadać **wiele przepływów** pracy w repozytorium, z których każdy może wykonywać inny zestaw **kroków**.

> Można odwoływać się do przepływu pracy w ramach innego przepływu, zobacz "Ponowne wykorzystywanie workflow".

# Plik workflow

[źródło](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)

GitHub Actions wykorzystują składnię `YAML` do definiowania przepływu pracy. Każdy przepływ pracy przechowywany jest jako osobny plik `YAML` w repozytorium kodu, w katalogu o nazwie `.github/workflows`.

Elementy składni `YAML` dla workflow są opisane w [Workflow syntax](Workflow%20syntax.md).