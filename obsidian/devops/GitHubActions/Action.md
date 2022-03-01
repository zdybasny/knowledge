#github #pipelines

**Akcja** to niestandardowa aplikacja na platformie **GitHub Actions**, która wykonuje złożone, ale często powtarzane zadanie. Użyj **akcji**, aby zredukować ilość powtarzającego się kodu, który piszesz w swoich [plikach workflow](Workflow.md#Plik%20workflow).

**Akcja** może ściągnąć repozytorium git z GitHuba, skonfigurować poprawny toolchain dla Twojego środowiska budowania lub ustawić uwierzytelnianie do Twojego dostawcy chmury.

Możesz pisać własne **akcje** lub znaleźć je w [GitHub Marketplace](https://github.com/marketplace?type=actions), aby użyć ich w swoich [Workflowach](Workflow.md).

##### Spis treści:

- [Użycie akcji w workflow](#Użycie%20akcji%20w%20workflow)
  - [Użycie release managera](#Użycie%20release%20managera)
  - [Użycie wejścia i wyjścia](#Użycie%20wejścia%20i%20wyjścia)
- [Custom action](#Custom%20action)
  - [Docker](#Docker)
  - [JavaScript](#JavaScript)
  - [Composite](#Composite)

# Użycie akcji w workflow

## Dodawanie akcji

Aby dodać **akcję** do [Workflow](Workflow.md), należy się do niej odwołać z **kroku** w [pliku Workflow](Workflow.md#Plik%20workflow), używając: [**`jobs.<job_id>.steps[*].uses`**](Workflow%20syntax.md#jobs%20job_id%20steps%20uses).

**Akcje**, do których odwołuje się [Workflow](Workflow.md), można wyświetlić jako zależności na wykresie zależności repozytorium zawierającego dany [Workflow](Workflow.md).

## Użycie wejścia i wyjścia

Dane weściowe do akcji podaje się z użyciem [`jobs.<job_id>.steps[*].with`](Workflow%20syntax.md#jobs%20job_id%20steps%20with) (dla [Action](Action.md) w JavaScript) lub [`jobs.<job_id>.steps[*].with.args`](Workflow%20syntax.md#jobs%20job_id%20steps%20with%20args) (dla skonteneryzowego [Action](Action.md)).

Aby uzyskać wartość wyjścia **akcji** w **kroku**, należy użyć: `steps.<step_id>.output.<output_id>`.

Aby zobaczyć dane wejściowe i wyjściowe **akcji**, sprawdź plik `action.yml` lub `action.yaml` w katalogu głównym repozytorium.

Przykład pliku:

```yaml
name: "Example"
description: "Receives file and generates output"
inputs:
  file-path: # id of input
    description: "Path to test script"
    required: true
    default: "test-file.js"
outputs:
  results-file: # id of output
    description: "Path to results file"
```

# Custom action

Można używać **custom actions** pod pewnymi obostrzeniami w zależności od tego, na jakim repozytorium one się znajdują:

- `private` - tylko workflow na tym samym repozytorium co akcja,
- `internal` - tylko repozytoria w ramach organizacji,
- `public` - bez ograniczeń.

**Akcje** wymagają pliku metadanych, aby zdefiniować wejścia, wyjścia i główny entypoint do nich. Nazwa pliku metadanych musi być albo `action.yml` albo `action.yaml`.

GitHub obsługuje akcje zbudowane jako:

- kontener Docker,
- akcje JavaScript,
- akcje composite.

## Docker

[źródło](https://docs.github.com/en/actions/creating-actions/creating-a-docker-container-action)

Kontenery Docker pakują środowisko z kodem **akcji**, tworząc ją bardziej spójną i niezawodną, ponieważ konsument akcji nie musi się martwić o narzędzia i zależności.

Ze względu na opóźnienie w pobieraniu i budowaniu kontenera, takie **akcje** są wolniejsze niż akcje JavaScript.

Skonteneryzowane **akcje** mogą być wykonywane tylko na [Runnerach](Runner.md) z systemem operacyjnym **Linux**. [Self-hosted Runnery](Runner.md#Self-hosted%20Runner) muszą mieć dodatkowo zainstalowany Docker.

Obraz ze skonteneryzowaną można pozyskać na 2 spoby:

- budując jego obraz przy wywołaniu: ``,
- pobierając obraz z rejestru Dockera.

## JavaScript

[źródło](https://docs.github.com/en/actions/creating-actions/creating-a-javascript-action)

**Akcje JavaScript** mogą być uruchamiane bezpośrednio na [Runnerze](Runner.md) i oddzielają kod **akcji** od środowiska użytego do jej uruchomienia. Wykonują się szybciej niż akcja skonteneryzowana.

Aby zapewnić, że **akcje JavaScript** są kompatybilne ze wszystkimi [GitHub-hosted Runnerami](Runner.md#GitHub-hosted%20Runner) (Ubuntu, Windows i macOS), spakowany kod JavaScript, który powinien być czystym JavaScriptem i nie powinien opierać się na innych binariach, gdzyż akcje te używają binariów, które już istnieją w wirtualnym środowisku.

> Zestaw narzędzi [**GitHub Actions Toolkit**](https://github.com/actions/toolkit) zapewnia pakiety, które pozwalają uprościć integrację z GitHub Actions.

## Composite

Złożona akcja (**composite action**) pozwala na łączenie wielu **kroków** [Workflow](Workflow.md) w ramach jednej **akcji**.
