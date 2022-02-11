#github #pipelines

[źródło](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)

GitHub Actions wykorzystują składnię `YAML` do definiowania [Workflow](Workflow.md). Każdy [Workflow](Workflow.md) przechowywany jest jako osobny plik `YAML` w repozytorium kodu, w katalogu o nazwie `.github/workflows`.

##### Spis treści

- [Składnia Workflow:](#składnia%20workflow)
  - [name](#name)
  - [on](#on)
    - [on.<event_name>.types](#on%20event_name%20types)
    - [on.<event_name>.<filter_name>](#on%20event_name%20filter_name)
    - [on.schedule](#on%20schedule)
    - [on.workflow_call](#on%20workflow_call)
      - [on.workflow_call.inputs](#on%20workflow_call%20inputs)
      - [on.workflow_call.outputs](#on%20workflow_call%20outputs)
      - [on.workflow_call.secrets](#on%20workflow_call%20secrets)
    - [on.workflow_dispatch](#on%20workflow_dispatch)
    - [on.workflow_dispatch.inputs](#on%20workflow_dispatch%20inputs)
  - [permissions](#permissions)
  - [env](#env)
  - [defaults](#defaults)
  - [concurrency](#concurrency)
- [Składnia Jobs:](#składnia-jobs)
  - [Atrybuty Jobs analogiczne do Workflow](#atrybuty%20jobs%20analogiczne%20do%20workflow)
    - [jobs.<job_id>.name](#jobs%20job_id%20name)
    - [jobs.<job_id>.permissions](#jobs%20job_id%20permissions)
    - [jobs.<job_id>.concurrency](#jobs%20job_id%20concurrency)
    - [jobs.<job_id>.outputs](#jobs%20job_id%20outputs)
    - [jobs.<job_id>.env](#jobs%20job_id%20env)
    - [jobs.<job_id>.defaults](#jobs%20job_id%20defaults)
  - [jobs.<job_id>.runs-on](#jobs%20job_id%20runs-on)
  - [jobs.<job_id>.needs](#jobs%20job_id%20needs)
  - [jobs.<job_id>.if](#jobs%20job_id%20if)
  - [jobs.<job_id>.environment](#jobs%20job_id%20environment)
  - [jobs.<job_id>.outputs](#jobs%20job_id%20outputs-1)
  - [jobs.<job_id>.timeout-minutes](#jobs%20job_id%20timeout-minutes)
  - [jobs.<job_id>.strategy](#jobs%20job_id%20strategy)
    - [jobs.<job_id>.strategy.matrix](#jobs%20job_id%20strategy%20matrix)
      - [jobs.<job_id>.strategy.matrix.include](#jobs%20job_id%20strategy%20matrix%20include)
      - [jobs.<job_id>.strategy.matrix.exclude](#jobs%20job_id%20strategy%20matrix%20exclude)
    - [jobs.<job_id>.strategy.max-parallel](#jobs%20job_id%20strategy%20max-parallel)
    - [jobs.<job_id>.strategy.fail-fast](#jobs%20job_id%20strategy%20fail-fast)
  - [jobs.<job_id>.continue-on-error](#jobs%20job_id%20continue-on-error)
  - [jobs.<job_id>.container](#jobs%20job_id%20container)
  - [jobs.<job_id>.services](#jobs%20job_id%20services)
  - [jobs.<job_id>.uses](#jobs%20job_id%20uses)
  - [jobs.<job_id>.with](#jobs%20job_id%20with)
  - [jobs.<job_id>.secrets](#jobs%20job_id%20secrets)
- [Składnia Steps:](#składnia-steps)
  - [Atrybuty Steps analogiczne do Jobs](#atrybuty%20steps%20analogiczne%20do%20jobs)
    - [jobs.<job_id>.steps[*].name](#jobs%20job_id%20steps%20name) 
    - [jobs.<job_id>.steps[*].uses](#jobs%20job_id%20steps%20uses)
    - [jobs.<job_id>.steps[*].env](#jobs%20job_id%20steps%20env)
    - [jobs.<job_id>.steps[*].continue-on-error](#jobs%20job_id%20steps%20continue-on-error)
    - [jobs.<job_id>.steps[*].timeout-minutes](#jobs%20job_id%20steps%20timeout-minutes)
  - [jobs.<job_id>.steps[*].run](#jobs%20job_id%20steps%20run)
  - [jobs.<job_id>.steps[*].shell](#jobs%20job_id%20steps%20shell)
  - [jobs.<job_id>.steps[*].with](#jobs%20job_id%20steps%20with)
    - [jobs.<job_id>.steps[*].with.args](#jobs%20job_id%20steps%20with%20args)
    - [jobs.<job_id>.steps[*].with.entrypoint](#jobs%20job_id%20steps%20with%20entrypoint)

# Składnia Workflow:

Elementy składni `YAML` dla [Workflow](Workflow.md):

## name

GitHub wyświetla nazwy [Workflow](Workflow.md) na stronie [akcji](Action.md) w repozytorium.
Jeśli pominiesz nazwę, GitHub ustawi ją na ścieżkę do pliku [Workflow](Workflow.md) względem roota repozytorium.

> Możesz również nadać nazwę dla [Joba](Job.md):  [`jobs.<job_id>.name`](#jobs%20job_id%20name).

## on

Ddefiniuje, jakie zdarzenia (tu [lista zdarzeń](Event.md#Lista%20zdarzeń)) mogą powodować uruchomienie [Workflow](Workflow.md).

Można zdefiniować:

- pojedyncze [zdarzenie](Event.md): ` on: push  `,
  - jedno z wielu [zdarzeń](Event.md): `on: [push, fork]` (każdy [zdarzenie](Event.md) uruchamia nowy [Workflow](Workflow.md))
- harmonogram: [`on.schedule`](#on%20schedule),
- wywołanie przez inny [Workflow](Workflow.md) :  [`on.workflow_call`](#on%20workflow_call),
- wywołanie manualne: [`on.workflow_dispatch`](#on%20workflow_dispatch).

Można również ograniczyć wykonywanie [Workflow](Workflow.md), tak aby występował on tylko dla określonych plików, znaczników lub zmian w gałęziach. Opcje te opisane są w kolejnych sekcjach.

### on.<event_name>.types

Niektóre [zdarzenia](Event.md) posiadają typy aktywności, które dają większą kontrolę nad tym, kiedy [Workflow](Workflow.md) powinien zostać uruchomiony.

Użyj `on.<event_name>.types`, aby określić typ aktywności zdarzenia, który spowoduje uruchomienie [Workflow](Workflow.md).

Np. typ `created` zdarzenia `label`:

```yaml
on:
  label:
    types:
      - created
```

### on.<event_name>.<filter_name>

Niektóre [zdarzenia](Event.md) posiadają filtry, które dają większą kontrolę nad tym, kiedy [Workflow](Workflow.md) powinien zostać uruchomiony.

Np. [zdarzenie](Event.md) `push` posiada filtr `branches`:

```yaml
on:
  push:
    branches:
      - main
      - 'releases/**'
```

### on.schedule

Definiuje harmonogram dla swoich [Workflow](Workflow.md), używając składni [**POSIX cron**](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html), np.:

```yaml
on:
  schedule:
    # * is a special character in YAML so you have to quote this string
    - cron: '30 5 * * 1,3'
    - cron: '30 5 * * 2,4'
```

Najkrótszy interwał, w jakim można uruchamiać zaplanowane [Workflow](Workflow.md), to raz na 5 minut.

### on.workflow_call

Definiuje wejścia, wyjścia i sekrety dla [Workflow](Workflow.md) wielokrotnego użytku.

#### on.workflow_call.inputs

Określa dane wejściowe, które przekazywane są do wywoływanego [Workflow](Workflow.md).

Parametry wymagają podania typu `on.workflow_call.inputs.<input_id>.type`.

Jeśli parametr `default` nie jest ustawiony, domyślną wartością wejścia jest:

- `false` dla boolean,
- `0` dla liczby,
- `""` dla łańcucha.

Przykład:

```yaml
on:
  workflow_call:
    inputs:
      username:
        description: 'A username passed from the caller workflow'
        default: 'john-doe'
        required: false
        type: string

jobs:
  print-username:
    runs-on: ubuntu-latest

    steps:
      - name: Print the input name to STDOUT
        run: echo The username is ${{ inputs.username }}
```

#### on.workflow_call.outputs

Wyjścia wywołanego [Workflow](Workflow.md) są dostępne dla wszystkich potomnych [Jobów](Job.md) w wywołującym go [Workflow](Workflow.md).

Każde wyjście posiada:

- identyfikator;
- opcjonalny opis;
- wartość - musi być ustawiona na wartość wyjścia z [Joba](Job.md) ([`jobs.<job_id>.outputs`](#jobs%20job_id%20outputs)) w ramach wywołanego [Workflow](Workflow.md).

Przykład:

```yaml
on:
  workflow_call:
    # Map the workflow outputs to job outputs
    outputs:
      workflow_output1:
        description: "The first job output"
        value: ${{ jobs.my_job.outputs.job_output1 }}
      workflow_output2:
        description: "The second job output"
        value: ${{ jobs.my_job.outputs.job_output2 }}
```

#### on.workflow_call.secrets

W ramach wywołanego [Workflow](Workflow.md) można użyć kontekstu sekretów, by odwołać się do sekretu.

Jeśli wywołujący [Workflow](Workflow.md) przekaże sekret, który nie jest określony w wywołanym [Workflow](Workflow.md), spowoduje to błąd.

Przykład:

```yaml
on:
  workflow_call:
    secrets:
      access-token:
        description: 'A token passed from the caller workflow'
        required: false

jobs:
  pass-secret-to-action:
    runs-on: ubuntu-latest

    steps:
      - name: Pass the received secret to an action
        uses: ./.github/actions/my-action
        with:
          token: ${{ secrets.access-token }}
```

### on.workflow_dispatch

Aby móc wywołac [Workflow](Workflow.md) manualnie, należy zawrezć `on.workflow_dispatch`.

### on.workflow_dispatch.inputs

Dodaje parametry do [Workflow](Workflow.md) (analogicznie do [`on.workflow_dispatch.inputs`](#on%20workflow_call%20inputs)).

Przykład:

```yaml
on:
  workflow_dispatch:
    inputs:
      logLevel:
        description: 'Log level'
        required: true
        default: 'warning' 
        type: choice
        options:
        - info
        - warning
        - debug 
```

## permissions

#TODO

> Możesz również ustawić uprawnienia na poziomie [Joba](Job.md):  [`jobs.<job_id>.permissions`](#jobs%20job_id%20permissions).

## env

Można również ustawić zmienne środowiskowe, które są dostępne tylko dla:

- kroków wszystkich [Jobów](Job.md) w [Workflow](Workflow.md): `env`,
- kroków pojedynczego [Joba](Job.md): `jobs.<job_id>.env`,
- pojedynczego kroku: `jobs.<job_id>.steps[*].env`.

Przykład:

```yaml
env:
  SERVER: production
```

> Możesz również ustawić zmienne środowiskowe na poziomie:
>
> - [Joba](Job.md):  [`jobs.<job_id>.env`](#jobs%20job_id%20env),
> - **kroku**:  [`jobs.<job_id>.steps[*]*.env`](#jobs%20job_id%20steps%20env).
>
> Zmienne w **kroku** przysłaniają zmienne w [Jobie](Job.md), a te przysłaniają zmienne [Workflow](Workflow.md).

## defaults

Tworzy mapę ustawień domyślnych, które będą stosowane do wszystkich [Jobów](Job.md) w [Workflow](Workflow.md).

> Można również określić ustawienia domyślne, które dostępne są tylko dla danego [Joba](Job.md): [`jobs.<job_id>.defaults`](#jobs%20job_id%20defaults)

Przykład:

```yaml
defaults:
  run:
    shell: bash
    working-directory: scripts
```

## concurrency

Użycie **współbieżności** zapewnia, że w danym momencie uruchomione zostanie tylko tylko [Job](Job.md) lub [Workflow](Workflow.md) korzystający z tej samej grupy współbieżności. Grupa współbieżności może być dowolnym ciągiem znaków lub [Expression](Action.md#Expression). Wyrażenie może używać tylko [github context](Action.md#github%20context).

> Możesz również określić współbieżność na poziomie [Joba](Job.md):  [`jobs.<job_id>.concurrency`](#jobs%20job_id%20concurrency).

Przykład:

```yaml
concurrency: ci-${{ github.ref }}
```

# Składnia Jobs:

[źródło](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)

Można uruchomić nieograniczoną liczbę [Jobów](Job.md), o ile nie przekroczy się limitów użycia [Workflow](Workflow.md).

## Atrybuty Jobs analogiczne do Workflow

Większość [atrybutów workflow](#Plik%20workflow) są analogiczne dla opisu [Jobów](Event.md):

### jobs.<job_id>.name

[`name`](#name)

> Możesz również nadać nazwę dla **kroku**:  [`jobs.<job_id>.steps[*].name`] [jobs job_id steps name](#jobs%20job_id%20steps%20name).

### jobs.<job_id>.permissions

[`permissions`](#permissions)

### jobs.<job_id>.concurrency

[`concurrency`](#concurrency)

### jobs.<job_id>.outputs

[`on.workflow_call.outputs`](#on%20workflow_call%20outputs)

### jobs.<job_id>.env

[`env`](#env)

> Możesz również ustawić zmienne środowiskowe na poziomie **kroku**:  [`jobs.<job_id>.steps[*]*.env`](#jobs%20job_id%20steps%20env).
> Zmienne w **kroku** przysłaniają zmienne w [Jobie](Job.md).

### jobs.<job_id>.defaults

[`defaults`](#defaults)

## jobs.<job_id>.runs-on

[źródło](https://docs.github.com/en/actions/using-jobs/choosing-the-runner-for-a-job)

Każdy [Job](Event.md) działa w środowisku [Runnera](Runner.md) określonym przez `runs-on`:

```yaml
runs-on: ubuntu-latest
```

Istnieje też możliwość do wskazania [self-hosted runner](Runner.md#self-hosted%20runner) poprzez jego etykietę:

```yaml
runs-on: [self-hosted-label, linux]
```

## jobs.<job_id>.needs

Przebieg [Workflow](Workflow.md) składa się z jednego lub więcej [Jobów](Job.md), które domyślnie uruchamiane są równolegle. Aby uruchomić [Joby](Job.md) sekwencyjnie, można zdefiniować zależności od innych [Jobów](Job.md), używając słowa kluczowego `jobs.<job_id>.needs`.

```yaml
jobs:
  job1:
  job2:
    needs: job1
  job3:
    needs: [job1, job2]
```

## jobs.<job_id>.if

[źródło](https://docs.github.com/en/actions/using-jobs/using-conditions-to-control-job-execution)

Sprawdza warunek, który decyduje o uruchomieniu [Joba](Job.md). Możesz użyć dowolnego obsługiwanego [kontekstu](Action.md#github%20context) i [Expression](Action.md#Expression) do utworzenia warunku.

Przykład:

```yaml
name: example-workflow
on: [push]
jobs:
  production-deploy:
    if: github.repository == 'octo-org/octo-repo-prod'
    runs-on: ubuntu-latest
    steps:
 ...
```

## jobs.<job_id>.environment

[źródło](https://docs.github.com/en/actions/using-jobs/using-environments-for-jobs)

Określa [Environment](Environment.md), do którego odwołuje się [Job](Job.md).

Można przekazać [Environment](Environment.md) jako:

- tylko nazwę środowiska

```yaml
environment: staging_environment
```

- lub obiekt środowiska z `name` i `url`.

```yaml
environment:
  name: production_environment
  url: ${{ steps.step_id.outputs.url_output }}
```

## jobs.<job_id>.outputs

[źródło](https://docs.github.com/en/actions/using-jobs/defining-outputs-for-jobs)

Tworzy mapę wyjść dla danego [Joba](Job.md). Dane wyjściowe [Joba](Job.md) są dostępne dla wszystkich kolejnych [Jobów](Job.md), które zależą od tego [Joba](Job.md) ([`jobs.<job_id>.needs`](#jobs%20job_id%20needs)).

Przykład:

```yaml
jobs:
  job1:
    runs-on: ubuntu-latest
    # Map a step output to a job output
    outputs:
      output1: ${{ steps.step1.outputs.test }}
      output2: ${{ steps.step2.outputs.test }}
    steps:
      - id: step1
        run: echo "::set-output name=test::hello"
      - id: step2
        run: echo "::set-output name=test::world"
  job2:
    runs-on: ubuntu-latest
    needs: job1
    steps:
      - run: echo ${{needs.job1.outputs.output1}} ${{needs.job1.outputs.output2}}
```

## jobs.<job_id>.timeout-minutes

Maksymalna liczba minut, przez którą [Job](Job.md) może być wykonywane zanim GitHub automatycznie je anuluje. Domyślnie: `360`.

> Możesz również ustawić timeout dla **kroku**: [`jobs.<job_id>.steps[*]*.timeout-minutes`](#jobs%20job_id%20steps%20timeout-minutes).

## jobs.<job_id>.strategy

[źródło](https://docs.github.com/en/actions/using-jobs/using-a-build-matrix-for-your-jobs)

### jobs.<job_id>.strategy.matrix

Ddefiniuje macierz różnych konfiguracji [Jobów](Job.md). Pozwala na tworzenie wielu [Jobów](Job.md) poprzez wykonanie zastępowania zmiennych w pojedynczej definicji [Joba](Job.md).

> Matryca może wygenerować maksymalnie 256 [Jobów](Job.md) na jeden przebieg [Workflow](Workflow.md).

Kolejność, w jakiej definiujesz macierz ma znaczenie. Pierwsza zdefiniowana opcja będzie pierwszym [Jobem](Job.md), które zostanie uruchomione w przepływie pracy.

#### jobs.<job_id>.strategy.matrix.include

`include` dodaje nowe zmienne z przypisanymi im wartośćiami jeśli kombinacja zotanie przypasowana.

Poniższy przykład wykona się 9 razy, w tym taz ze zmienna `npm`

```yaml
runs-on: ${{ matrix.os }}
strategy:
  matrix:
    os: [macos-latest, windows-latest, ubuntu-18.04]
    node: [10, 12, 14]
    include:
      # includes a new variable of npm with a value of 6
      # for the matrix leg matching the os and version
      - os: windows-latest
        node: 8
        npm: 6
```

Jeśli podany zestaw w `include` nie pasuje do żadnej kombinacji z macierzy, zostanie on dodany jako kolejna kombinacja.

#### jobs.<job_id>.strategy.matrix.exclude

Wszystkie kombinacje `include` są przetwarzane po `exclude`, co pozwala dąłaczyć daną kombinację ponownie po wykluczeniu.

### jobs.<job_id>.strategy.max-parallel

Określa maksymalną liczbę [Jobów](Job.md), które mogą być uruchomione jednocześnie podczas korzystania z macierzowej strategii pracy.

### jobs.<job_id>.strategy.fail-fast

Jeśli ustawiony na `true`, GitHub anuluje wszystkie [Joby](Job.md) `in-progress`, jeśli choć jeden [Job](Job.md) z  `matrix` się nie powiedzie. Domyślnie: `true`.

## jobs.<job_id>.continue-on-error

Jeśli ustawiony na `true`, GitHub pozwala na przejście [Workflow](Workflow.md), gdy to [Job](Job.md) nie powiedzie się.

> Możesz również ustawić atrybut ten na poziomie **kroku**: [`jobs.<job_id>.steps[*]*.continue-on-error`](#jobs%20job_id%20steps%20continue-on-error), który chroni [Job](Job.md) przez niepowodzeniem jego **kroku**.

## jobs.<job_id>.container

[źródło](https://docs.github.com/en/actions/using-jobs/running-jobs-in-a-container)

Tworzy kontener do uruchomienia wszystkich kroków w [Jobie](Job.md), które nie określają jeszcze kontenera.

Jeśli masz kroki, które używają zarówno skryptu jak i kontenera, działania kontenera będą uruchamiane jako kontenery rodzeństwa w tej samej sieci z tymi samymi uchwytami woluminów.

Jeśli nie ustawisz `container`, wszystkie kroki będą uruchamiane bezpośrednio na hoście określonym przez `runs-on`, chyba że krok odnosi się do akcji skonfigurowanej do uruchamiania w kontenerze.

Przykład:

```yaml
jobs:
  my_job:
    container:
      image: node:14.16
      env:
        NODE_ENV: development
      ports:
        - 80
      volumes:
        - my_docker_volume:/volume_mount
      options: --cpus 1
```

Atrybuty `jobs.<job_id>.container` to:

- `image`,
- `credentrials`,
- `env`,
- `ports`,
- `volumes`,
- `options` (dla [opcji `docker create`](https://docs.docker.com/engine/reference/commandline/create/#options)).

## jobs.<job_id>.services

[źródło](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idservices)

Służy do hostowania kontenerów usług dla [Joba](Job.md) w [Workflow](Workflow.md). Kontenery usług są przydatne do tworzenia np. baz danych lub usług cache'owania, takich jak Redis. [Runner](Runner.md) automatycznie tworzy sieć Docker i zarządza cyklem życia kontenerów usług.

`jobs.<job_id>.services` posiada te same atrybuty co [`jobs.<job_id>.container`](#jobs%20job_id%20container).

## jobs.<job_id>.uses

[źródło](https://docs.github.com/en/actions/using-workflows/reusing-workflows#calling-a-reusable-workflow)

Wywołuje [Workflow](Workflow.md) wielokrotnego użytku.

Inaczej niż w przypadku używania [Action](Action.md) w ramach **kroku** ([`jobs.<job_id>.steps[*].uses`](#jobs%20job_id%20steps%20uses)), [Workflow](Workflow.md) wielokrotnego użytku wywoływane są bezpośrednio w ramach **Job**, a nie z jego kroków.

Odwołanie do [plików workflow](#Plik%20workflow) wielokrotnego użytku następuje przy użyciu jednej z poniższych składni:

- `{owner}/{repo}/{path}/{filename}@{ref}` dla [Workflow](Workflow.md) w publicznym repozytorium,
- `./{path}/{filename}` dla [Workflow](Workflow.md) w tym samym repozytorium i tym samym commicie.

`{ref}` może być SHA, znacznikiem wydania lub nazwą gałęzi.
Użycie SHA z commitów jest najbezpieczniejsze dla stabilności i bezpieczeństwa

Przykład:

```yaml
jobs:
  call-workflow-1-in-local-repo:
    uses: octo-org/this-repo/.github/workflows/workflow-1.yml@172239021f7ba04fe7327647b213799853a9eb89
  call-workflow-2-in-local-repo:
    uses: ./.github/workflows/workflow-2.yml
  call-workflow-in-another-repo:
    uses: octo-org/another-repo/.github/workflows/workflow.yml@v1
```

## jobs.<job_id>.with

Przekazuje mapę wejść do wywoływanego [Workflow](Workflow.md).

Wszystkie dane wejściowe, które przekazujemy, muszą pasować do specyfikacji danych wejściowych zdefiniowanych w wywołanym [Workflow](Workflow.md) ([`on.workflow_call.inputs.<input_id>`](#on%20workflow_call%20inputs) ).

> W przeciwieństwie do [`jobs.<job_id>.steps[*].with`](#jobs%20job_id%20steps%20with), dane wejściowe przekazane za pomocą `jobs.<job_id>.with` nie są dostępne jako zmienne środowiskowe w wywołanym [Workflow](Workflow.md). Zamiast tego, można się do nich odwoływać za pomocą kontekstu `inputs`.

Przykład:

```yaml
jobs:
  call-workflow:
    uses: octo-org/example-repo/.github/workflows/called-workflow.yml@main
    with:
      username: mona
```

## jobs.<job_id>.secrets

Przekazuje mapę sekretów do wywoływania [Workflow](Workflow.md).

Przekazywane sekrety muszą odpowiadać nazwom zdefiniowanym w wywoływanym [Workflow](Workflow.md).

Przykład:

```yaml
jobs:
  call-workflow:
    uses: octo-org/example-repo/.github/workflows/called-workflow.yml@main
    secrets:
      access-token: ${{ secrets.PERSONAL_ACCESS_TOKEN }}
```

# Składnia Steps:

[źródło](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idsteps)

[Job](Job.md) zawiera sekwencję **kroków**: `steps`. Kroki mogą uruchamiać:

- polecenia powłoki;
- zadania konfiguracyjne;
- [Actions](Action.md) w:
  - tym samym repozytorium,
  - repozytorium publicznym,
  - opublikowaną w rejestrze Docker.

Nie wszystkie **kroki** uruchamiają [Action](Action.md), ale wszystkie [Action](Action.md) uruchamiają się jako **krok**. Każdy **krok** działa w swoim własnym procesie w środowisku uruchomieniowym i ma dostęp do przestrzeni roboczej i systemu plików. Ponieważ **kroki** działają w swoim własnym procesie, zmiany w zmiennych środowiskowych nie są zachowywane pomiędzy **krokami**. GitHub dostarcza wbudowanych **kroki** do konfiguracji i wykonania [Jobów](Job.md).

## Atrybuty Steps analogiczne do Jobs

### jobs.<job_id>.steps[*].name

Jeśli nie podasz `name`, nazwa kroku będzie domyślnie odpowiadać tekstowi podanemu w poleceniu [`jobs.<job_id>.steps[*].run`](#jobs%20job_id%20steps%20run).

[`jobs.<job_id>.name`](#jobs%20job_id%20name)

### jobs.<job_id>.steps[*].uses

Inaczej niż w przypadku wywoływania [Workflow](Workflow.md) wielokrotnego użytku bezpośrednio przez [Job](Job.md) ([`jobs.<job_id>.uses`](#jobs%20job_id%20uses)), `uses` w **kroku** wywołuje [Action](Action.md).

### jobs.<job_id>.steps[*].env

[`jobs.<job_id>.env`](#jobs%20job_id%20env)

### jobs.<job_id>.steps[*].continue-on-error

[`jobs.<job_id>.continue-on-error`](#jobs%20job_id%20continue-on-error)

### jobs.<job_id>.steps[*].timeout-minutes

[`jobs.<job_id>.timeout-minutes`](#jobs%20job_id%20timeout-minutes)

## jobs.<job_id>.steps[*].run

Uruchamia programy wiersza poleceń przy użyciu powłoki systemu operacyjnego.

Każde słowo kluczowe `run` reprezentuje nowy proces i powłokę w środowisku [Runnera](Runner.md). Gdy podajemy polecenia wielowierszowe, każdy wiersz uruchamiany jest w tej samej powłoce. Polecenia domyślnie uruchamiane są przy użyciu powłoki innej niż powłoka logowania (**non-login shell**).

> Można zmienić powłokę, o czym w [`jobs.<job_id>.steps[*].shell`](#jobs%20job_id%20steps%20shell)

Używając słowa `working-directory`, można określić katalog roboczy, w którym ma zostać uruchomione polecenie.

Przykład:

```yaml
- name: Clean install dependencies and build
  run: |
    npm ci
    npm run build
  working-directory: ./app
```

## jobs.<job_id>.steps[*].shell

Zastąpuje domyślne ustawienia powłoki w systemie operacyjnym [Runnera](Runner.md).

Polecenie powłoki wykonuje plik tymczasowy, zawierający polecenia określone słowem kluczowym [`jobs.<job_id>.steps[*].run`](#jobs%20job_id%20steps%20run).

## jobs.<job_id>.steps[*].with

Przekazuje mapę parametrów wejściowych do [Action](Action.md). Każdy parametr jest parą klucz-wartość.

Parametry są ustawiane jako zmienne środowiskowe z prefiksem `INPUT_` i conwercją _upper case_.

A `map` of the input parameters defined by the action. Each input parameter is a key/value pair. Input parameters are set as environment variables. The variable is prefixed with `INPUT_` and converted to upper case.

> W przeciwieństwie do [`jobs.<job_id>.with`](#jobs%20job_id%20with), dane wejściowe przekazane za pomocą `jobs.<job_id>.steps[*].with` nie można się do nich odwoływać za pomocą kontekstu `inputs`.

### jobs.<job_id>.steps[*].with.args

Łańcuch określający `CMD` kontenera Docker przekazywane args podczas jego uruchamiania.

Przykład:

```yaml
steps:
  - name: Explain why this job ran
    uses: octo-org/action-name@main
    with:
      entrypoint: /bin/echo
      args: The ${{ github.event_name }} event triggered this step.
```

### jobs.<job_id>.steps[*].with.entrypoint

Łańcuch nadpisujący `ENTRYPOINT` kontenera Docker przekazywane args podczas jego uruchamiania.

W przeciwieństwie do instrukcji `ENTRYPOINT` Dockera, która posiada formę **shell** i **exec**,
`entrypoint` akceptuje tylko pojedynczy string określający plik wykonywalny do uruchomienia.

Przykład:

```yaml
steps:
  - name: Run a custom command
    uses: octo-org/action-name@main
    with:
      entrypoint: /a/different/executable
```
