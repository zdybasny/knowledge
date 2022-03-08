#github #pipelines

[źródło](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment)

**Środowiska** używane są do opisania ogólnego celu [wdrożenia](CD.md), takiego jak `production`, `staging`, `UAT` czy `development`. Kiedy [Workflow](Workflow.md) wdraża się do **środowiska**, jest ono wyświetlane na stronie głównej repozytorium.

Użycie **środowisk** pozwala zwiększyć kontrolę nad [wdrożeniami](CD.md) poprzez:

- wymaganie zatwierdzenia [Joba](Job.md) aby go wykonać;
- ograniczenie, które gałęzie mogą wyzwalać [Workflow](Workflow.md);
- lub ograniczenie dostępu do sekretów.

> Użucie środowisk jest możliwe tylko dla repozytoriów `public` oraz `enterprise`.

##### Spis treści

- [Użycie środowisk w workflow](#Użycie%20środowisk%20w%20workflow)
- [Reguły ochrony środowiska](#Reguły%20ochrony%20środowiska)
  - [Required reviewers](#Required%20reviewers)
  - [Wait timer](#Wait%20timer)
  - [Deployment branches](#Deployment%20branches)
- [Sekrety środowiska](#Sekrety%20środowiska)

# Użycie środowisk w workflow

Każdy [Job](Job.md) w [Workflow](Workflow.md) może odwoływać się do jednego **środowiska**.

Wszelkie [Reguły ochrony środowiska](#Reguły%20ochrony%20środowiska) skonfigurowane dla danego **środowiska** muszą zostać spełnione, zanim [Job](Job.md) odwołujący się do tego **środowiska** zostanie wysłany do [Runnera](Runner.md). Dopiero po tym [Job](Job.md) może uzyskać dostęp do sekretów **środowiska**.

Gdy [Workflow](Workflow.md) odwołuje się do **środowiska**, pojawi się ono we wdrożeniach repozytorium.

> Użycie [Environment](Environment.md) w [Workflow](Workflow.md) definiuje się poprzez  [`jobs.<job_id>.environment`](Workflow%20syntax.md#jobs%20job_id%20environment).

# Reguły ochrony środowiska

Reguły wymagają spełnienia określonych warunków, zanim [Job](Job.md) odwołujący się do **środowiska** będzie mógł zostać wykonany.

## Required reviewers

Wymagani recenzenci (**reviewer**) to osoby lub zespoły, które muszą zatwierdzać [Joby](Job.md) dla [Workflow](Workflow.md) odwołujące się do **środowiska**.

Na liście **recenzentów** można umieścić do sześciu użytkowników lub zespołów. Recenzenci muszą mieć co najmniej dostęp do repozytorium w trybie odczytu. Tylko jeden z wymaganych recenzentów musi zatwierdzić [Job](Job.md), aby był ono kontynuowany.

> Więcej: [**About protected branches**](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/defining-the-mergeability-of-pull-requests/about-protected-branches).

## Wait timer

Licznik opóźnia [Job](Job.md) o określony czas po jego początkowym uruchomieniu.

Czas (w minutach) musi być liczbą całkowitą z zakresu od 0 do 43 200 (30 dni).

## Deployment branches

Można ograniczyć, które branche mogą zostać wdrożone do **środowiska**. Dostępne opcje:

- **All branches** - wszystkie branche w repozytorium mogą być wdrażone;
- **Protected branches** - tylko branche z włączonymi regułami ochrony branchy mogą zostać wdrożone; lub wszyskie jeśli nie zdefiniowano żadnej reguły;
- **Selected branches** - tylko branche, które pasują do określonych wzorców nazw, mogą zostać wdrożone.

# Sekrety środowiska

**Sekrety** przechowywane w **środowisku** są dostępne **TYLKO** dla [Jobów](Job.md), które odwołują się do tego **środowiska**. Jeśli środowisko wymaga zatwierdzenia, [Job](Job.md) nie może uzyskać dostępu do **sekretów środowiska**, dopóki nie zatwierdzi go jeden z [wymaganych recenzentów](#Required%20reviewers).

> **Uwaga**: [Workflow](Workflow.md), który działa na [Self-hosted Runner](Runner.md#Self-hosted%20Runner), nie jest uruchamiany w izolowanym kontenerze, nawet jeśli korzysta ze **środowisk**. Więcej: [**Hardening for self-hosted runners**](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#hardening-for-self-hosted-runners).
