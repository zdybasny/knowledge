#github #pipelines #cd

[źródło](https://docs.github.com/en/actions/deployment/about-deployments/about-continuous-deployment)

##### Spis treści

- [Użycie środowisk](#Użycie%20środowisk)
- [Użycie współbieżności](#Użycie%20współbieżności)
- [Użycie starter workflow](#Użycie%20starter%20workflow)
- [Monitoring](#Monitoring)
- [Wyświetlanie plakietki stanu](#Wyświetlanie%20plakietki%20stanu)
- [Wdrożenia do chmury publicznej](#Wdrożenia%20do%20chmury%20publicznej)

# Użycie środowisk

Użycie [Environment](Environment.md) pozwala zwiększyć kontrolę nad wdrożeniami poprzez:

- wymagać zatwierdzenia [Joba](Job.md) aby go wykonać;
- ograniczać, które gałęzie mogą wyzwalać [Workflow](Workflow.md);
- lub ograniczać dostęp do sekretów.

> Użycie [Environment](Environment.md) w [Workflow](Workflow.md) definiuje się poprzez  [`jobs.<job_id>.environment`](Workflow%20syntax.md#jobs%20job_id%20environment).
> Więcej: [**Użycie środowisk w workflow**](Environment.md#Użycie%20środowisk%20w%20workflow).

# Użycie współbieżności

Można użyć współbieżności (**concurrency**), aby ograniczyć pipeline CD do maksymalnie jednego wdrożenia `in-progress` i jednego oczekującego (`pending`) na wdrożenie.

> Gdy uruchomiony zostanie [Workflow](Workflow.md), zostanie on wstrzymany ze statusem `pending`, jeżeli trwa (status `in progress`) jakiekolwiek inny [Job](Job.md) ([`jobs.<job_id>.concurrency`](Workflow%20syntax.md#jobs%20job_id%20concurrency)) lub [Workflow](Workflow.md) ([`concurrency`](Workflow%20syntax.md#concurrency)), który wykorzystuje tę samą grupę współbieżności. Spowoduje on również anulowanie każdego [Joba](Job.md) i [Workflow](Workflow.md), który korzysta z tej smej grupy współbieżności i ma status `pending`.

Można również użyć `cancel-in-progress`, aby anulować każde aktualnie uruchomiony [Job](Job.md) lub [Workflow](Workflow.md) w tej samej grupie współbieżności.

```yaml
name: Deployment

concurrency: 
  group: production
  cancel-in-progress: true
```

# Użycie starter workflow

GitHub oferuje szablony [Workflow](Workflow.md) (**starter workflow**) dla CD do kilku popularnych serwisów (np. Azure Web App).

# Monitoring

Kiedy [Workflow](Workflow.md) wdraża się do [Environment](Environment.md), [Environment](Environment.md) ten jest wyświetlany na głównej stronie repozytorium.

> Więcej: [**Viewing deployment history**](https://docs.github.com/en/actions/deployment/managing-your-deployments/viewing-deployment-history).

Każde uruchomienie [Workflow](Workflow.md) generuje w czasie rzeczywistym wykres, który ilustruje jego postęp. Wykres ten można wykorzystać do monitorowania i usuwania błędaów we wdrożeniach. Więcej informacji na ten temat można znaleźć w punkcie "Korzystanie z wykresu wizualizacji".

> Więcej: [**Using the visualization graph**](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/using-the-visualization-graph).

Można również przeglądać logi każdego uruchomienia [Workflow](Workflow.md) oraz historię ich uruchomień.

> Więcej: [**Using the visualization graph**](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/viewing-workflow-run-history).

# Wyświetlanie plakietki stanu

Można użyć plakietki stanu (**status badge**), aby wyświetlić status [Workflow](Workflow.md) np. w `READNE.md` lub na stronie internetowej.

Można również wyświetlić status [Workflow](Workflow.md) uruchomionego dla konkretnego brancha lub [Eventu](Event.md), używając parametrów zapytania URL.

> Więcej: [**Adding a workflow status badge**](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/adding-a-workflow-status-badge)

# Wdrożenia do chmury publicznej

Instrukcje i przykładowe wdrożewnia do chmury publicznej napopularniejszych dostwców:

- [Deploying to Amazon Elastic Container Service](https://docs.github.com/en/actions/deployment/deploying-to-your-cloud-provider/deploying-to-amazon-elastic-container-service),
- [Deploying to Azure](https://docs.github.com/en/actions/deployment/deploying-to-your-cloud-provider/deploying-to-azure),
- [Deploying to Google Kubernetes Engine](https://docs.github.com/en/actions/deployment/deploying-to-your-cloud-provider/deploying-to-google-kubernetes-engine).
