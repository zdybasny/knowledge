#github #pipelines

##### Spis treści

- [Best practices](#Best%20practices)
  - [Sekrety](#Sekrety)
  - [Pozostałe](#Pozostałe)
- [GITHUB_TOKEN](#GITHUB_TOKEN)
  - [Domyślnie uprawnienia i zakresy](#Domyślnie%20uprawnienia%20i%20zakresy)
  - [Modyfikacja uprawnień](#Modyfikacja%20uprawnień)
- [Personal access token](#Personal%20access%20token)

# Best practices

[źródło](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)

## Sekrety

- Nie używaj ustrukturyzowanych adnych (np. JSON) jako sekret.

Bardzo łatwo mogą zmienić postać (np. biały znak) i ominąć maskowanie.

- Rejestruj wszystkie sekrety jako secrets.

Skorzystaj w tym celu z `core.setSecret(myPassword);` lub `echo '::add-mask::${myPassword}` (więcej o formacie: [Workflow command](Workflow.md#Workflow%20command)). Zwłaszcza nowe, wygenerowane i przetworzone wartości, np. Base64.

- Używaj credentiali o minimalnych wymaganych uprawnieniach.

[Akcje](Action.md) mogą używać [`GITHUB_TOKEN`](#GITHUB_TOKEN) poprzez dostęp do niego z [Context](Context.md) `github.token`. Dlatego dobrą praktyką jest ustawienie domyślnych uprawnień dla [`GITHUB_TOKEN`](#GITHUB_TOKEN) na dostęp tylko do odczytu dla zawartości repozytorium. Uprawnienia te mogą być następnie zwiększane, w zależności od potrzeb, dla poszczególnych zadań w ramach pliku przepływu pracy.

- Przeglądaj i rotuj sekrety.

Z okresowego przeglądu może wyniknąć, że niektóre sekrety nie są już potrzebne. Z kolei rotacja sekretów zmniejsza okres, kiedy to zdemaskowane sekrety są ważne.

- Wymagaj przeglądu (review) dostępu do sekretów.

Do ochrony sekretów [Environment](Environment.md) można użyć recenzentów, bez zatwierdzenia których [Job](Job.md) nie może uzyskać dostępu do sekretów.

## Pozostałe

- Minimalizuj ryzyko wstrzyknięcia skryptu (**script injection**).

Zawsze należy brać pod uwagę, czy kod może wykonać niezaufane dane wejściowe. Może się to zdarzyć, gdy atakujący doda do danych wejściowych (np. do [Context](Context.md), nazwy brancha, e-mail) złośliwe komendy i skrypty. Powinieneś upewnić się, że wartości te nie wpływają bezpośrednio do [Workflow](Workflow.md), [Action](Action.md), wywołań API lub gdziekolwiek indziej, gdzie mogłyby zostać zinterpretowane jako kod wykonywalny.

Dobre praktyki monimalizowania takiego ryzyka zostały przedstawione w [Good practices for mitigating script injection attacks](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#good-practices-for-mitigating-script-injection-attacks).

- Użyj [`CODEOWNERS`](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners), aby kontrolować zmiany w [plikach workflow](Workflow.md#Plik%20workflow).

Np. jeśli wszystkie [pliki workflow](Workflow.md#Plik%20workflow) zxnajdują się w `.github/workflows`, można dodać ten katalog do listy właścicieli kodu, tak aby wszelkie zmiany w tych plikach wymagały najpierw zatwierdzenia przez wyznaczonego recenzenta.

- Użyj OpenID Connect u dostwacy chmury, jeśli taką opcję zapewnia.
  Jeśli [Action](Action.md) musi mieć dostęp do zasobów dostawcy chmury, który obsługuje **OpenID Connect** ([**OIDC**](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect)), możesz skonfigurować [Workflow](Workflow.md) tak, aby uwierzytelniały się bezpośrednio u dostawcy chmury. Pozwoli to zrezygnować z przechowywania credentiali i zapewni inne korzyści związane z bezpieczeństwem.

- Dostęp cross-reposytory
  [Actions](Action.md) są celowo przypisane do jednego repozytorium w danym momencie. Użytkownicy mają określone uprawnienia do każdego repozytorium, więc zezwolenie na `GITHUB_TOKEN` lub TOKEN dla jednego repozytorium, aby przyznać dostęp do innego, wpłynęłoby na model uprawnień GitHub, jeśli nie zostanie wdrożony ostrożnie.

# GITHUB_TOKEN

Na początku każdego [Workflow](Workflow.md) GitHub automatycznie tworzy unikalny `secret.GITHUB_TOKEN`, który będzie używany w danym [Workflow](Workflow.md). Dostępny jest również poprzez [Context](Context.md) `github.token`. Token wygasa po zakończeniu zadania. Można go wykorzystać do uwierzytelniania się podczas pracy. Uprawnienia tokena są ograniczone do repozytorium, które zawiera dany [Workflow](Workflow.md) pracy.

Gdy [Workflow](Workflow.md) używa `GITHUB_TOKEN`, [Eventy](Event.md) zdarzenia przez ten `GITHUB_TOKEN` nie będą tworzyły nowego [Workflow](Workflow.md), aby zapobiec przypadkowej rekurencyji. Jeśli jednak potrzeba, aby **workflow** uruchomił inny **wrokflow**, należy użyć [PAT](#Personal%20access%20token) zamiast `secrets.`[`GITHUB_TOKEN`](Security.md#GITHUB_TOKEN) do wyzwalania [Eventów](Event.md) wymagających tokena.

## Domyślnie uprawnienia i zakresy

Osoby z uprawnieniami administratora dla:

- **enterprise**,
- **organization**,
- **repository**

mogą ustawić **domyślne uprawnienia** na:

- **Read and write permissions** (_permisywne_) - uprawnienia odczytu i zapisu w repozytorium dla wszystkich zakresów (`metadata` tylko do odczytu);
- **Read repository contents permission** (_restrykcyjne_) - uprawnie tylko do odczytu w repozytorium dla zawartości `contents` i `metadata`.

Lista zakresów (scopes) wraz z uprawnieniami znajduje się na stronie: [Permissions for the GITHUB_TOKEN](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#permissions-for-the-github_token).

## Modyfikacja uprawnień

Uprawnienia dla `GITHUB_TOKEN` można modyfikować w [plikach workflow](Workflow.md#Plik%20workflow).

Jeżeli domyślne uprawnienia są _permisywne_, można edytować [plik workflow](Workflow.md#Plik%20workflow), usuwając niektóre z nich z `GITHUB_TOKEN`. Dobrą praktyką bezpieczeństwa jest nadawanie `GITHUB_TOKEN` najmniej wymaganego dostępu.

Jeśli domyślne uprawnienia dla `GITHUB_TOKEN` są _restrykcyjne_, konieczne może być podniesienie poziomu uprawnień, aby umożliwić pomyślne wykonanie niektórych akcji i poleceń. Jeśli potrzebujesz token, który wymaga uprawnień, które nie są dostępne w `GITHUB_TOKEN`, możesz utworzyć [Personal access token](#Personal%20access%20token) i ustawić go jako sekret w swoim repozytorium.

Zmianę uprawnień dla poszczególnych [Workflowów](Workflow.md) i [Jobów](Job.md) dokonuje się z użyciem odpowiednio:

- [`permissions`](Workflow%20syntax.md#permissions),
- [`jobs.<job_id>.permissions`](Workflow%20syntax.md#jobs%20job_id%20permissions).

Gdy używany jest `permissions`, wszystkie nieokreślone uprawnienia ustawione są na `none`
(z wyjątkiem zakresu `metadata`, który zawsze otrzymuje dostęp `read`).

Uprawnienia, które posiadał `GITHUB_TOKEN` dla danego [Joba](Job.md), można sprawdzić w sekcji `Set up job` w logach [Workflow](Workflow.md).

# Personal access token

[źródło](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)

Personal access tokeny (**PAT**) są alternatywą dla używania haseł do uwierzytelniania w GitHubie podczas korzystania z interfejsu API GitHuba lub wiersza poleceń. **PAT** tworzą się i przechowują je jako sekrety oraz mogą być łatwo usuwane (dostęp tymczasowy) i rotowane.

> Jako środek ostrożności GitHub automatycznie usuwa PAT, które nie były używane od roku. Aby zapewnić dodatkowe bezpieczeństwo, zdecydowanie zalecamy dodanie wygaśnięcia do osobistych tokenów dostępu.

Token bez przypisanych zakresów (**scope**) może uzyskać dostęp tylko do informacji publicznych. Zakresy możliwe do przypisania można znaleść na [**Available scopes**](https://docs.github.com/en/developers/apps/building-oauth-apps/scopes-for-oauth-apps#available-scopes).

Przykład użycia **PAT**:

```yaml
on:
  issues:
    types:
      - opened

jobs:
  label_issue:
    runs-on: ubuntu-latest
    steps:
      - env:
          GITHUB_TOKEN: ${{ secrets.MY_TOKEN }}
          ISSUE_URL: ${{ github.event.issue.html_url }}
        run: |
          gh issue edit $ISSUE_URL --add-label "triage"
```
