#github #pipelines

Wykonawca (**Runner**) jest serwerem, który uruchamia [Workflows](Workflow.md). Każdy **runner** może uruchomić jeden [Job](Job.md) w jednocześnie.

GitHub udostępnia na świeżej, nowo dostarczonej maszynie wirtualnej następujące **runnery**:

- Ubuntu Linux,
- Microsoft Windows,
- macOS.

> Jeśli potrzebny jest inny system operacyjny lub specyficzna konfiguracja sprzętowa, można [uruchomić własny runner](#Hostowanie%20własnych%20runnerów).

##### Spis treści

- [Użycie runnera w workflow](#Użycie%20runnera%20w%20workflow)
- [GitHub-hosted Runner](#github-hosted-runner)
  - [Software](#Software)
  - [Hardware](#Hardware)
  - [System plików](#System%20plików)
    - [System plików w kontenerze](#System%20plików%20w%20kontenerze)
- [Self-hosted Runner](#self-hosted-runner)

# Użycie runnera w workflow

[Workflow](Workflow.md) można uruchamiać bezpośrednio na maszynie wirtualnej lub w kontenerze Docker.

Można określić typ **runnera** dla każdego [Joba](Job.md) w [Workflow](Workflow.md). Każdy [Job](Job.md) wykonywany jest na świeżej instancji maszyny wirtualnej. Wszystkie kroki w [Joboe](Job.md) wykonywane są w tej samej instancji maszyny wirtualnej, co pozwala [Akcjom](Action.md) w tym [Jobie](Job.md) dzielić się informacjami za pomocą systemu plików.

# GitHub-hosted Runner

[źródło](https://docs.github.com/en/enterprise-cloud@latest/actions/using-github-hosted-runners/about-github-hosted-runners)

**GitHub-hosted runner** to maszyna wirtualna hostowana przez GitHub z zainstalowaną aplikacją GitHub Actions runner. GitHub oferuje runnery z systemami operacyjnymi Linux, Windows i macOS.

## Software

[źródło](https://docs.github.com/en/enterprise-cloud@latest/actions/using-github-hosted-runners/about-github-hosted-runners#preinstalled-software)

Logi [Workflow](Workflow.md) zawierają link do pre-instalowanych narzędzi na danym [Workflow](Workflow.md). Aby znaleźć te informacje w logu, należy rozwinąć sekcję `Set up job` > `Virtual Environmnet` > link `Included Software`

Runnery hostowane na GitHubie zawierają:

- domyślne narzędzia wbudowane w system operacyjny
- oraz pakiety wymienione `<OS>-Readme.md` w repozytorium: [actions/virtual-environments](https://github.com/actions/virtual-environments/tree/main/images).

Jeśli brakuje jakiegoś pakietu, można do doinstalować na czas uruchomienia [Joba](Job.md):

- [Linux](https://github.com/actions/virtual-environments/blob/main/images/linux/Ubuntu2004-Readme.md): `apt`,
- [macOS](https://github.com/actions/virtual-environments/blob/main/images/macos/macos-11-Readme.md#package-management): `brew`,
- [Windows](https://github.com/actions/virtual-environments/blob/main/images/win/Windows2022-Readme.md#package-management): `choco`.

## Hardware

GitHub hostuje **runnery** dla systemów Linux i Windows na maszynach wirtualnych **Standard_DS2_v2** w Microsoft Azure z zainstalowaną aplikacją runnera GitHub Actions. Aplikacja uruchomieniowa hostowana przez GitHub jest rozwinięciem Azure Pipelines Agent.

> Przychodzące pakiety ICMP są blokowane dla wszystkich maszyn wirtualnych Azure, więc polecenia ping lub traceroute mogą nie działać.

Specyfikacja sprzętu: [Supported runners and hardware resources](https://docs.github.com/en/enterprise-cloud@latest/actions/using-github-hosted-runners/about-github-hosted-runners#supported-runners-and-hardware-resources).

## System plików

GitHub wykonuje[Actions](Action.md) i komendy powłoki w określonych katalogach na maszynie wirtualnej, które nie są statyczne. Użyj zmiennych środowiskowych udostępnianych przez GitHub:

- `HOME` - katalog domowy zawiera dane użytkownika,
- `GITHUB_WORKSPACE` - w tym katalogu wykonywane są [Action](Action.md) i komendy powłoki,
- `GITHUB_EVENT_PATH` - zawartość `POST` zdarzenia webhook, które wywołało przepływ pracy (GitHub przepisuje go za każdym razem, gdy wykonywana jest akcja, aby odizolować zawartość plików pomiędzy akcjami).

### System plików w kontenerze

[Actions](Action.md) uruchamiane w kontenerach Docker mają statyczne katalogi pod ścieżką `/github`:

- `/github/home`,
- `/github/workspace`,
- `/github/workflow`.

Zaleca się jednak używanie zmiennych środowiskowych  `HOME`, `GITHUB_WORKSPACE`, `GITHUB_EVENT_PATH`.

## Prawa admina

Maszyny wirtualne dla systemów **Linux** i **macOS** działają z wykorzystaniem bezhasłowego `sudo`.

Maszyny wirtualne **Windows** są skonfigurowane do uruchamiania jako administratorzy z wyłączoną User Account Control (**UAC**).

# Self-hosted Runner

[źródło](https://docs.github.com/en/actions/hosting-your-own-runners)

**Samodzielnie utrzymywane runnery** oferują większą kontrolę nad sprzętem, systemem operacyjnym i narzędziami programowymi niż **runnery** uruchamiane na GitHubie.

Z **uruchamianiem samodzielnym** możesz wybrać niestandardową konfigurację sprzętową z większą mocą obliczeniową lub pamięcią, aby uruchomić większe zadania, zainstalować oprogramowanie dostępne w sieci lokalnej i wybrać system operacyjny, który nie jest oferowany przez uruchamianie obsługiwane przez GitHub.

**Self-hosted runners** mogą być:

- fizyczne,
- wirtualne,
- w kontenerze,
- on-premises,
- w chmurze.

Mmożna je dodawać na różnych poziomach hierarchii zarządzania:

- repozytorium - dedykowane dla pojedynczego repozytorium,
- organizacji - mogą przetwarzać zadania dla wielu repozytoriów w organizacji,
- przedsiębiorstwa -  mogą być przypisani do wielu organizacji na koncie przedsiębiorstwa.

Maszyna **runnera** łączy się z GitHub Enterprise Cloud za pomocą aplikacji open-osurce -[actions/runner](https://github.com/actions/runner).

**Self-hosted runner** jest automatycznie usuwany z GitHub Enterprise Cloud, jeśli nie łączy się z GitHub Actions przez ponad 30 dni.
