#github #pipelines

**Zadanie** (**job**) to zbiór **kroków** dla [Workflow](Workflow.md), które wykonywane są przez tego samego wykonawcę.
Każdy **krok** jest:

- albo skryptem powłoki, który zostanie wykonany,
- albo [Action](Action.md), która zostanie uruchomiona.

Kroki wykonywane są w kolejności na tym samym [Runnerze](Runner.md), współdzieląc dane.

Można skonfigurować zależności **zadania** z innymi **zadaniami**. Domyślnie **zadania** nie mają żadnych zależności i działają równolegle do siebie. Gdy zadanie ma zależność od innego zadania, będzie ono czekać na zakończenie zadania zależnego zanim będzie mogło zostać uruchomione.

##### Spis treści

- [jobs: w pliku workflow](#jobs%20w%20pliku%20workflow)
- [Cache](#Cache)
  - [Dependency caching](#Dependency%20caching)
  - [Package manager caching](#Package%20manager%20caching)
  - [Artifact storing](#Artifact%20storing)
- [Service containers](#Service%20containers)
  - [Sieć Dockera w jobie](#Sieć%20Dockera%20w%20jobie)

# jobs: w pliku workflow

[źródło](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)

W ramach pojedynczego [Workflow](Workflow.md) można uruchomić nieograniczoną liczbę **jobów**, o ile nie przekroczy się limitów użycia [Workflow](Workflow.md).

Składnia i atrybuty **jobów** opisane są w rodziale [**Workflow syntax > jobs**](Workflow%20syntax.md#jobs).

# Cache

## Dependency caching

[źródło](https://docs.github.com/en/actions/advanced-guides/caching-dependencies-to-speed-up-workflows)

[GitHub-hosted Runnery](Runner.md#GitHub-hosted%20Runner) są uruchamiane jako świeże środowiska dla każdego **joba**, więc jeśli **joby** regularnie używają zależności, możesz rozważyć cashowanie plików, aby poprawić wydajność często używanych **przepływów**.

Po utworzeniu pamięci cach, jest ona dostępna dla wszystkich **przepływów pracy** w tym samym repozytorium.

Aby cashować zależności dla **joba**, musisz użyć [`actions/cashe`](https://github.com/actions/cache). [Action](Action.md) ta pobiera pamięć podręczną identyfikowaną przez unikalny klucz.

Ten przykład demonstruje, jak buforować katalog `~/.npm`:

```yaml
jobs:
  example-job:
    steps:
      - name: Cache node modules
        uses: actions/cache@v2
        env:
          cache-name: cache-node-modules
        with:
          path: ~/.npm
          key: ${{ runner.os }}-build-${{ env.cache-name }}-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-build-${{ env.cache-name }}-
```

## Package manager caching

Jeśli buforujesz menedżery pakietów, rozważ użycie odpowiednich  `actions/setup-*`, które wymagają prawie zerowej konfiguracji i są łatwe w użyciu:

| Package manager | [Action](Action.md)                                               |
| --------------- | ----------------------------------------------------------------- |
| npm, yarn, pnpm | [`actions/setup-node`](https://github.com/actions/setup-node)     |
| pip, pipenv     | [`actions/setup-python`](https://github.com/actions/setup-python) |
| gradle, maven   | [`actions/setup-java`](https://github.com/actions/setup-java)     |
| ruby gems       | [`actions/setup-ruby`](https://github.com/ruby/setup-ruby)        |

Artefakty i buforowanie są podobne, ponieważ dają możliwość przechowywania plików na GitHubie, ale każda z tych funkcji oferuje inne przypadki użycia i nie mogą być używane zamiennie.

## Artifact storing

Z `cache` należy korzystać wtedy, gdy chcemy ponownie wykorzystać pliki, które nie zmieniają się często pomiędzy **jobami** lub [Workflowami](Workflow.md).

Z artefaktów należy korzystać, gdy chcemy zachować pliki wytworzone przez zadanie, aby móc je przeglądać po zakończeniu [Workflow](Workflow.md).

# Service containers

[źródło](https://docs.github.com/en/enterprise-cloud@latest/actions/using-containerized-services)

Można skonfigurować kontenery usług (**service containers**) dla każdego **joba** w przepływie, używając słowa kluczowego `services`. GitHub niszczy go po zakończeniu **joba**. **Kroki** w zadaniu mogą komunikować się z wszystkimi kontenerami usług, które są częścią tego samego zadania.

```yaml
name: Redis container example
on: push

jobs:
  # Label dla `container-job`
  container-job:
    #  Kontener musi być uruchomiony na Linux-based OS
    runs-on: ubuntu-latest
    # Obraz z Docker Hub, w którym jest uruchamiany `container-job`
    container: node:10.18-jessie

    - # Kontenery usług do uruchomienia z `container-job`
    services:
      # Label użyty do uzyskania dostępu do usługi kontenera
      redis:
        # Obraz z Docker Hub
        image: redis
```

Więcej o składni dla kontenerów znajduje się w [`jobs.<job_id>.container`](Workflow%20syntax.md#jobs%20job_id%20container) i [`jobs.<job_id>.services`](Workflow%20syntax.md#jobs%20job_id%20services).

## Sieć Dockera w jobie

GitHub łączy kontenery z **jobem** za pomocą zdefiniowanych przez użytkownika [bridge networks](https://docs.docker.com/network/bridge/) Dockera.

Nie musisz konfigurować żadnych portów dla kontenerów. Domyślnie wszystkie kontenery, które są częścią tej samej sieci Dockera, udostępniają sobie nawzajem wszystkie porty, a żadne porty nie są udostępniane poza siecią Dockera.
