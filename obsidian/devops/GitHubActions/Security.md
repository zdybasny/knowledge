#github #pipelines

##### Spis treści

- [Best practices](#Best%20practices)
	- [Sekrety](#Sekrety)
	- [Pozostałe](#Pozostałe)
- [GITHUB_TOKEN](#GITHUB_TOKEN)

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

#TODO
