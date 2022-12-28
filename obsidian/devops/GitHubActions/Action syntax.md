#github #pipelines

[źródło](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions#runsusing)

Wszystkie [Actions](Action.md) wymagają pliku metadanych `action.yml` albo `action.yaml`. Dane w pliku metadanych definiują wejścia, wyjścia i konfigurację uruchomień dla [Action](Action.md).

##### Spis treści

- [Atrybuty wspólne:](#atrybuty-wspólne)
  - [name](#name)
  - [author](#author)
  - [description](#description)
  - [inputs.\<input\_id\>](#inputsinput_id)
    - [inputs.\<input\_id\>.description](#inputsinput_iddescription)
    - [inputs.\<input\_id\>.require](#inputsinput_idrequire)
    - [inputs.\<input\_id\>.default](#inputsinput_iddefault)
    - [inputs.\<input\_id\>.deprecationMessage](#inputsinput_iddeprecationmessage)
  - [outputs](#outputs)
    - [outputs.\<output\_id\>](#outputsoutput_id)
      - [outputs.\<output\_id\>.description](#outputsoutput_iddescription)
      - [outputs.\<output\_id\>.value](#outputsoutput_idvalue)
  - [runs](#runs)
    - [runs.using](#runsusing)
  - [branding](#branding)
- [Atrybuty akcji w JavaScript:](#atrybuty-akcji-w-javascript)
  - [inputs](#inputs)
  - [runs](#runs-1)
    - [runs.main](#runsmain)
    - [runs.pre](#runspre)
    - [runs.pre-if](#runspre-if)
    - [runs.post](#runspost)
    - [runs.post-if](#runspost-if)
- [Atrybuty akcji w Dockerze:](#atrybuty-akcji-w-dockerze)
  - [inputs](#inputs-1)
  - [runs](#runs-2)
    - [runs.image](#runsimage)
    - [runs.env](#runsenv)
    - [runs.pre-entrypoint](#runspre-entrypoint)
    - [runs.entrypoint](#runsentrypoint)
    - [runs.post-entrypoint](#runspost-entrypoint)
    - [runs.pre-if](#runspre-if-1)
    - [runs.post-if](#runspost-if-1)
    - [runs.args](#runsargs)
- [Atrybuty akcji composite:](#atrybuty-akcji-composite)
  - [inputs](#inputs-2)
  - [outputs.\<output\_id\>.value](#outputsoutput_idvalue-1)
  - [runs](#runs-3)
    - [runs.steps](#runssteps)
      - [runs.steps\[\*\].run](#runsstepsrun)
      - [runs.steps\[\*\].shell](#runsstepsshell)
      - [runs.steps\[\*\].if](#runsstepsif)
      - [runs.steps\[\*\].name](#runsstepsname)
      - [runs.steps\[\*\].id](#runsstepsid)
      - [runs.steps\[\*\].env](#runsstepsenv)
      - [runs.steps\[\*\].working-directory](#runsstepsworking-directory)
      - [runs.steps\[\*\].uses](#runsstepsuses)
      - [runs.steps\[\*\].with](#runsstepswith)

# Atrybuty wspólne:

## name

`wymagany`

GitHub wyświetla tę nazwę w zakładce `actions`, aby pomóc wizualnie zidentyfikować [Actions](Action.md) w każdym [Jobie](Job.md).

## author

`opcjonalny`

Autor [akcji](Action.md).

## description

`wymagany`

Krótki opis [akcji](Action.md).

## inputs.<input_id>

`wymagany` dla `inputs` (dla [JavaScript](#Atrybuty%20akcji%20w%20JavaScript#inputs), [Docker](#Atrybuty%20akcji%20w%20Dockerze#inputs), [composite](#Atrybuty%20akcji%20composite#inputs))

### inputs.<input_id>.description

`wymagany` dla [`inputs.<input_id>`](#inputs%20input_id)

Opis `string` parametrów wejściowych.

### inputs.<input_id>.require

`wymagany` dla [`inputs.<input_id>`](#inputs%20input_id)

Typ `boolean`, który określa, czy parametr jest wymagany.

### inputs.<input_id>.default

`opcjonalny` dla [`inputs.<input_id>`](#inputs%20input_id)

Domyślna wartość `string`.

### inputs.<input_id>.deprecationMessage

`opcjonalny` dla [`inputs.<input_id>`](#inputs%20input_id)

Loguje `string` z wiadomością jako warning.

## outputs

`opcjonalny`

**Parametry wyjściowe** pozwalają [Akcji](Action.md) zadeklarować dane, które [Akcje](Action.md) uruchamiane w dalszej części [Workflow](Workflow.md) mogą korzystać

Jeśli nie zadeklarowano `outputs`, to nadal można je ustawić i używać w [Workflow](Workflow.md) poprzez użycie [Workflow command](Workflow.md#Workflow%20command).

### outputs.<output_id>

`wymagany` dla [`outputs`](#outputs)

Identyfikator typu `string` unikalny w ramach `outputs`.

Przykład:

```yaml
outputs:
  random-number:
    description: "Random number"
    value: ${{ steps.random-number-generator.outputs.random-id }}
runs:
  using: "composite"
  steps:
    - id: random-number-generator
      run: echo "::set-output name=random-id::$(echo $RANDOM)"
      shell: bash
```

#### outputs.<output_id>.description

`wymagany` dla [`outputs.<output_id>`](#outputs%20output_id)

Opis `string` parametru wyjściowego.

#### outputs.<output_id>.value

`wymagany` dla [`outputs.<output_id>`](#outputs%20output_id)

Wartość, na którą zostanie zmapowany parametr wyjściowy. Można ją ustawić na `string` lub [Expression](Workflow%20syntax.md#Expression) z [Context](Context.md).

## runs

`wymagany`

Określa typ [Action](Action.md) poprzez [`runs using`](#runs%20using)

- [JavaScript](#Atrybuty%20akcji%20w%20JavaScript#runs),
- [Docker](#Atrybuty%20akcji%20w%20Dockerze#runs),
- [composite](#Atrybuty%20akcji%20composite#runs)

oraz sposób wykonania akcji.

### runs.using

`wymagany`

Określa środowisko uruchomieniowe do wykonania kodu [Action](Action.md) poprzez wartości:

- `node12` / `node16`,
- `docker`,
- `composite`.

## branding

`opcjonalny`

[Ikona](https://feathericons.com) (`branding.icon`) w wybranym kolorze (`branding.color`) wyświetlana obok nazwy.

# Atrybuty akcji w JavaScript:

Poniżej znajdują się atrybuty dla [`runs.using: 'node12'`](#runs%20using) i [`runs.using: 'node16'`](#runs%20using).

## inputs

`opcjonalny`

**Parametry wejściowe** pozwalają na określenie danych, które [Action](Action.md) ma wykorzystać podczas działania. GitHub przechowuje parametry wejściowe jako zmienne środowiskowe. Nazwy tych zmiennych [`intputs.<input_id>`](#inputs%20input_id) są konwertowane do `INPUT_<UPPERCASE_INPUT_ID>`.

> Zaleca się używanie małych liter w identyfikatorach wejściowych, gdyż i tak są duże litery są konwertowane na małe.

## runs

`wymagany`

> Więcej: [Atrybuty wspólne  > runs](#Atrybuty%20wspólne#runs).

Przykład:

```yaml
runs:
  using: 'node16'
  pre: 'setup.js'
  pre-if: runner.os == 'linux'
  main: 'index.js'
  post: 'cleanup.js'
```

### runs.main

`wymagany`

Plik, który zawiera kod [Action](Action.md). Plik ten jest wykonywany przez runtime (określony w [`runs.using`](#runs%20using)).

### runs.pre

`opcjonalny`

Umożliwia uruchomienie skryptu na początku [Action](Action.md), zanim rozpocznie się akcja [`runs.main`](#runs%20main).

> Np. można użyć `pre` do uruchomienia skryptu konfiguracji warunków wstępnych.

Plik ten zostanie wykonany przez runtime określony za pomocą składni [`runs.using`](#runs%20using).

Domyślnie `pre` zawsze jest uruchamiane, ale można ją zastąpić, używając runs.pre-if [`runs.pre-if`](#Atrybuty%20akcji%20w%20JavaScript#runs%20pre-if).

### runs.pre-if

`opcjonalny`

W `pre-if` funkcje sprawdzają stan stan [Joba](Job.md) zadania, a nie stan samej [Action](Action.md).

Akcja [`runs.pre`](#runs%20pre) zostanie wykonana tylko wtedy, gdy spełnione zostaną warunki określone w `pre-if`. Jeśli nie jest ustawione, to domyślnym ustawieniem `pre-if` jest `always()`.

[Context](Context.md) **kroku** jest niedostępny, ponieważ żadne **kroki** nie zostały jeszcze wykonane.

### runs.post

`opcjonalny`

Umożliwia uruchomienie skryptu na końcu [Action](Action.md), po zakończeniu akcji [`runs.main`](#runs%20main).

> Np. można użyć `post` do zakończenia pewnych procesów lub usunięcia niepotrzebnych plików.

Plik ten zostanie wykonany przez runtime określony za pomocą składni [`runs.using`](#runs%20using).

### runs.post-if

`opcjonalny`

> Analogicznie do [`runs.pre-if`](#runs%20pre-if).

Sprawdza warunek do uruchomienia [`runs.post`](#runs%20post), a jeśli nie podano `post-if` to domyślnie jest `always()`.

# Atrybuty akcji w Dockerze:

Poniżej znajdują się atrybuty dla [`runs.using: 'docker'`](#runs%20using).

## inputs

`opcjonalny`

Aby dostarczyć `inputs` do kontenera, należy przekazać je poprzez `runs.args` lub `runs.env`.

## runs

`wymagany`

> Więcej: [Atrybuty wspólne  > runs](#Atrybuty%20wspólne#runs).

Przykład:

```yaml
runs:
  using: 'docker'
  image: 'Dockerfile'
  args:
    - 'bzz'
  pre-entrypoint: 'setup.sh'
  pre-if: runner.os == 'linux'
  entrypoint: 'main.sh'
  post-entrypoint: 'cleanup.sh'
```

### runs.image

`wymagany`

Obraz Dockera, który ma zostać użyty jako kontener do uruchomienia [Action](Action.md).

Wartością może być:

- nazwa podstawowego obrazu Dockera,
- lokalny plik `Dockerfile` w repozytorium
  - (plik mu się nazywać `Dockerfile` i trzeba użyć ścieżki względnej do `action.yaml`),
- publiczny obraz w Docker Hub lub innym rejestrze.

### runs.env

`opcjonalny`

Określa mapę klucz-wartość zmiennych środowiskowych do ustawienia w środowisku kontenera.

### runs.pre-entrypoint

`opcjonalny`

Umożliwia uruchomienie skryptu przed rozpoczęciem działania `entrypoint`.

GitHub Actions wykorzystuje do uruchomienia tej akcji `docker run` . Ponieważ uruchamia skrypt wewnątrz nowego kontenera, używając tego samego obrazu bazowego, stan uruchomienia jest inny niż w głównym kontenerze `entrypoint`. Dostęp do dowolnego stanu można uzyskać poprzez _workspace_, `HOME` lub jako zmienna `STATE_`.

Akcja `pre-entrypoint` domyślnie zawsze się uruchamia, o ile nie kontroluje jej [`runs.pre-if`](#Atrybuty%20akcji%20w%20Dockerze#runs%20pre-if).

### runs.entrypoint

`opcjonalny`

Ustawia instrukcję `ENTRYPOINT` pliku `Dockerfile` lub zastępuje istniejącą.

### runs.post-entrypoint

`opcjonalny`

Umożliwia uruchomienie skryptu czyszczącego po zakończeniu akcji [`runs.entrypoint`](#runs%20entrypoint).

Do uruchomienia tej akcji GitHub Actions używa `docker run`. Ponieważ uruchamia skrypt wewnątrz nowego kontenera, używając tego samego obrazu bazowego, stan uruchomienia jest inny niż w głównym kontenerze `entrypoint`. Dostęp do dowolnego stanu można uzyskać poprzez _workspace_, `HOME` lub jako zmienna `STATE_`.

Akcja `post-entrypoint` domyślnie zawsze się uruchamia, o ile nie kontroluje jej [`runs.post-if`](#Atrybuty%20akcji%20w%20Dockerze#runs%20post-if).

### runs.pre-if

`opcjonalny`

W `pre-if` funkcje sprawdzają stan stan [Joba](Job.md) zadania, a nie stan samej [Action](Action.md).

Akcja [`runs.pre-entrypoint`](#runs%20pre-entrypoint) zostanie wykonana tylko wtedy, gdy spełnione zostaną warunki określone w `pre-if`. Jeśli nie jest ustawione, to domyślnym ustawieniem `pre-if` jest `always()`.

### runs.post-if

`opcjonalny`

Akcja [`runs.post-entrypoint`](#runs%20post-entrypoint) zostanie wykonana tylko wtedy, gdy spełnione zostaną warunki określone w `post-if`. Jeśli nie jest ustawione, to domyślnym ustawieniem `post-if` jest `always()`.

### runs.args

`opcjonalny`

Tablica `string` określających wejścia dla kontenera Docker. Argumenty są używane zamiast instrukcji `CMD` w pliku `Dockerfile`. GitHub przekazuje te argumenty do `ENTRYPOINT` (jeśli został podany) kontenera podczas jego uruchamiania.

# Atrybuty akcji composite:

Poniżej znajdują się atrybuty dla [`runs.using: 'composite'`](#runs%20using).

> Dla uproszczenia dokumentacji [Composite action](Action.md#Composite) dalej będą zwane "Composite".

## inputs

`opcjonalny`

Composite nie otrzymuje automatycznie `INPUT_<UPPERCASE_INPUT_ID>`, dlatego można dane wejściowe podać ręcznie.

## outputs.<output_id>.value

`wymagany` dla [`outputs.<output_id>`](#outputs%20output_id)

Wartość, na którą parametr wyjściowy ma być zmapowany. Można ją ustawić jako `string` lub wyrażenie z [Context](Context.md).

Np. można użyć [Context](Context.md) kroku, aby ustawić wartość wyjścia na wartość wyjściową kroku:

```yaml
outputs:
  random-number:
    description: "Random number"
    value: ${{ steps.random-number-generator.outputs.random-id }}
runs:
  using: "composite"
  steps:
    - id: random-number-generator
      run: echo "::set-output name=random-id::$(echo $RANDOM)"
      shell: bash
```

## runs

`wymagany`

Konfiguruje ścieżkę do akcji composite.

> Więcej: [Atrybuty wspólne  > runs](#Atrybuty%20wspólne#runs).

### runs.steps

`wymagany`

#### runs.steps[*].run

`wymagany` wymiennie z [`runs.steps[*].uses`](#runs%20steps%20uses)

Polecenia, króra ma zostać wykonane. Może być _inline_ lub jako skrypt w repozytorium [Action](Action.md).

> Więcej: [Workflow syntax > `jobs.<job_id>.steps[*].uses`](Workflow%20syntax.md#jobs%20job_id%20steps%20uses).

Przykład:

```yaml
runs:
  using: "composite"
  steps:
    - run: ${{ github.action_path }}/test/script.sh
      shell: bash
```

#### runs.steps[*].shell

`opcjonalny`

Powłoka, w której ma zostać uruchomione polecenie podane w [`runs.steps[*].run`](#runs%20steps%20run).

> Więcej: [Workflow syntax > `jobs.<job_id>.steps[*].shell`](Workflow%20syntax.md#jobs%20job_id%20steps%20shell).

#### runs.steps[*].if

`opcjonalny`

Warunku `if` można użyć w celu uzależnienia uruchomienia **kroku** od określonego warunku.

> Więcej: [Workflow syntax > `jobs.<job_id>.steps[*].if`](Workflow%20syntax.md#jobs%20job_id%20steps%20if).

#### runs.steps[*].name

`opcjonalny`

Nazwa **kroku**.

> Więcej: [Workflow syntax > `jobs.<job_id>.steps[*].name`](Workflow%20syntax.md#jobs%20job_id%20steps%20name).

#### runs.steps[*].id

`opcjonalny`

Unikalny identyfikator **kroku**, który można używać do odwoływania się do **kroku** w [Context](Context.md).

> Więcej: [Workflow syntax > `jobs.<job_id>.steps[*].id`](Workflow%20syntax.md#jobs%20job_id%20steps%20id).

#### runs.steps[*].env

`opcjonalny`

Ustawia mapę zmiennych środowiskowych tylko dla tego **kroku**.

Jeśli chcesz zmodyfikować zmienną środowiskową przechowywaną w [Workflow](Workflow.md), użyj:
`echo "{name}={value}" >> $GITHUB_ENV`.

> Więcej: [Workflow syntax > `jobs.<job_id>.steps[*].env`](Workflow%20syntax.md#jobs%20job_id%20steps%20env).

#### runs.steps[*].working-directory

`opcjonalny`

Określa katalog roboczy, w którym zostanie uruchomione polecenie.

> Więcej: [Workflow syntax > `jobs.<job_id>.steps[*].working-directory`](Workflow%20syntax.md#jobs%20job_id%20steps%20working-directory).

#### runs.steps[*].uses

`wymagany` wymiennie z [`runs.steps[*].run`](#runs%20steps%20run)

Aby dodać [Action](Action.md) do [Workflow](Workflow.md), należy się do niej odwołać z **kroku**.

> Więcej: [Workflow syntax > `jobs.<job_id>.steps[*].uses`](Workflow%20syntax.md#jobs%20job_id%20steps%20uses).

#### runs.steps[*].with

`opcjonalny`

Przekazuje mapę parametrów wejściowych do [Action](Action.md). Każdy parametr jest parą klucz-wartość.

> Więcej: [Workflow syntax > `jobs.<job_id>.steps[*].with`](Workflow%20syntax.md#jobs%20job_id%20steps%20with).
