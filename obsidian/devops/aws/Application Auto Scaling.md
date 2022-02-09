#AWS #management #autoscaling
#containers #storage #compute #integration

[źródło](https://docs.aws.amazon.com/autoscaling/application/userguide/what-is-application-auto-scaling.html)

Application Auto Scaling jest usługą dostarczającą rozwiązania do automatycznego skalowania skalowalnych zasobów dla poszczególnych usług AWS poza Amazon EC2.

Application Auto Scaling pozwala na automatyczne skalowanie zasobów skalowalnych według zdefiniowanych przez Ciebie warunków w polisie:

- skalowanie na podstawie celu ([Target tracking scaling](#Target%20tracking%20scaling)) - skalowanie zasobu na podstawie wartości docelowej dla określonej metryki CloudWatch;
- skalowanie krokowe ([Step scaling](#Step%20scaling)) - skalowanie zasobu na podstawie zestawu korekt skalowania, które różnią się w zależności od wielkości naruszenia alarmu;
- skalowanie zaplanowane ([Scheduled scaling](#Scheduled%20scaling)) - skalowanie zasobu w oparciu o datę i godzinę.

Application Auto Scaling pozwala na skonfigurowanie automatycznego skalowania dla zasobów wymienionych na stronie: [**What is Application Auto Scaling?**](https://docs.aws.amazon.com/autoscaling/application/userguide/what-is-application-auto-scaling.html) Niektóre z nich są omówione poniżej.

##### Spis treści

- [Kluczowe pojęcia](#Kluczowe%20pojęcia)
  - [Scalable target](#Scalable%20target)
  - [Scaling policy](#Scaling%20policy)
    - [Cooldown](#Cooldown)
    - [Typy polisy skalowania](#Typy%20polisy%20skalowania)
      - [Target tracking scaling](#Target%20tracking%20scaling)
      - [Step scaling](#Step%20scaling)
      - [Scheduled scaling](#Scheduled%20scaling)
  - [Scheduled action](#Scheduled%20action)
- [Skalowanie zasobów](#Skalowanie%20zasobów)
  - [Aurora](#Aurora)
    - [Polityki skalowania](#Polityki%20skalowania)
- [See also](#See%20also)

# Kluczowe pojęcia

[źródło](https://docs.aws.amazon.com/autoscaling/application/userguide/getting-started.html)

## Scalable target

Jednostka, którą tworzysz w celu określenia zasobu, który chcesz skalować. Każdy skalowalny cel jest jednoznacznie identyfikowany przez:

- przestrzeń nazw usługi (**service namespace**),
- identyfikator zasobu (**resource ID**)
- oraz wymiar skalowalny (**scalable dimention**), który reprezentuje pewien wymiar pojemności usługi bazowej (zakres minimum-maksimum).

Np. [ECS](Elastic%20Container%20Services.md) obsługuje automatyczne skalowanie liczby zadań, tabela [DynamoDB](DynamoDB.md) obsługuje automatyczne skalowanie pojemności odczytu i zapisu tabeli oraz jej globalnych indeksów wtórnych, a [klaster Aurora](Aurora.md#Aurora%20DB%20Cluster) obsługuje skalowanie liczby replik.

## Scaling policy

Polityka skalowania nakazuje Application Auto Scaling śledzić określoną metrykę [CloudWatch](CloudWatch.md). Następnie określa, jakie działania skalujące należy podjąć, gdy metryka jest wyższa lub niższa od określonej wartości progowej.

Metryki, które są używane do automatycznego skalowania są publikowane przez usługę docelową, ale możesz również opublikować własną metrykę do [CloudWatch](CloudWatch.md), a następnie użyć jej z polityką skalowania.

> Polityka skalowania nigdy nie przekroczy lub nie zmniejszy zakresu minimum-maksimum [celu skalowania](#Scalable%20target). Można dokonywać zmian bezpośrednio w bazowym zasobie, które wykraczają poza ten zakres, o czym nie wie **Application Auto Scaling**. Jednak za każdym razem, gdy wywoływana jest polityka skalowania lub wywoływane jest API `RegisterScalableTarget`, **Application Auto Scaling** pobiera aktualną pojemność i porównuje ją z minimalną i maksymalną. Jeśli nie mieści się ona w zakresie minimum-maksimum, wówczas pojemność jest aktualizowana tak, aby była zgodna z ustawionym minimum i maksimum.

### Cooldown

Okres schładzania (**cooldown**) pomiędzy działaniami skalującymi pozwala zasobom ustabilizować się przed rozpoczęciem kolejnego działania skalującego. Automatyczne skalowanie aplikacji kontynuuje ocenę metryk podczas **cooldown'u**. Po zakończeniu okresu, polityka skalowania inicjuje kolejną czynność skalowania, jeśli jest ona potrzebna.

W czasie trwania okresu cooldown, jeśli konieczne jest większe skalowanie w oparciu o bieżącą wartość metryki, polityka skalowania skaluje natychmiast.

### Typy polisy skalowania

#### Target tracking scaling

[źródło](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-target-tracking.html)

Skalowanie zasobu na podstawie wartości docelowej dla określonej metryki [CloudWatch](CloudWatch.md).

Automatyczne skalowanie aplikacji tworzy i zarządza [alarmami CloudWatch](CloudWatch.md#Alarm), które wyzwalają politykę skalowania, oraz oblicza dostosowanie skalowania na podstawie metryki i wartości docelowej., aby utrzymywać obciążenie bliskie celu.

Różne sewisy posiadają różne predefiniowane metryki dostępne do użycia podczas tworzenia polityk skalowania śledzenia celu. Opcjonalnie można zdefiniować użycie niestandardowej specyfikacji metryki. Lista predefiniowanych  metryk znajduje się w dokumentacji: [Choosing metrics](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-target-tracking.html#available-metrics) oraz poniżej w opisach akalowania poszczególnych serwisów.

#### Step scaling

[źródło](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-step-scaling-policies.html)

Skalowanie zasobu na podstawie zestawu korekt skalowania, które różnią się w zależności od wielkości naruszenia [alarmu](CloudWatch.md#Alarm).

Możesz określić typ dostosowania jako procent aktualnej pojemności skalowalnego celu lub w liczbach bezwzględnych.

> Jeśli metryka skalowania jest metryką wykorzystania, która zwiększa lub zmniejsza się proporcjonalnie do pojemności skalowalnego celu, zalecamy użycie polityki [Target tracking scaling](#Target%20tracking%20scaling). 

> Nadal masz możliwość użycia skalowania śledzącego cel ze skalowaniem krokowym dla bardziej zaawansowanej konfiguracji polityki skalowania. Na przykład, jeśli chcesz, możesz skonfigurować bardziej agresywną reakcję, gdy wykorzystanie osiągnie pewien poziom.

Application Auto Scaling obsługuje następujące typy dostosowania dla polityk skalowania krokowego:

- **ChangeInCapacity** - zmienia bieżącą pojemność obiektu docelowego **o określoną** wartość,
- **ExactCapacity** - zmienia bieżącą pojemność obiektu docelowego **na określoną** wartość,
- **PercentChangeInCapacity** - zmienia bieżącą pojemność obiektu docelowego o podaną wartość procentową.

#### Scheduled scaling

[źródło](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-scheduled-scaling.html)

Skalowanie zasobu w oparciu o datę i godzinę, co pozwala na ustawienie własnego harmonogramu skalowania zgodnie z przewidywalnymi zmianami obciążenia.

Aby użyć skalowania zaplanowanego, należy utworzyć [Scheduled action](#Scheduled%20action)s. Możesz tworzyć zaplanowane akcje, które skalują się tylko raz lub skalują się według powtarzającego się harmonogramu.

W określonym czasie **Application Auto Scaling** skaluje się w oparciu o nowe wartości pojemności, porównując aktualną pojemność z określoną minimalną i maksymalną pojemnością:

- jeśli bieżąca pojemność jest mniejsza niż określona minimalna pojemność, skaluje (zwiększa pojemność) do określonej minimalnej pojemności;

- jeśli aktualna pojemność jest większa niż określona pojemność maksymalna, skaluje (zmniejsza pojemność) do określonej pojemności maksymalnej.

> Na tym samym zasobie można zastosować skalowanie zaplanowane i inne polityki skalowania, aby uzyskać korzyści z obu rozwiązań. Po uruchomieniu [Scheduled action](#Scheduled%20action), polityka skalowania może nadal podejmować decyzje o dalszym skalowaniu pojemności. Podczas gdy aplikacja skaluje się w [Scalable target](#Scalable%20target) do zapotrzebowania, bieżąca pojemność musi mieścić się w minimalnej i maksymalnej pojemności, która została ustawiona przez [Scheduled action](#Scheduled%20action).

## Scheduled action

**Akcje zaplanowane** automatycznie skalują zasoby w określonym dniu i czasie. Działają one poprzez modyfikację minimalnej i maksymalnej pojemności dla [Scalable target](#Scalable%20target), dlatego mogą być używane do skalowania in i out zgodnie z harmonogramem poprzez ustawienie minimalnej pojemności na wysokim poziomie lub maksymalnej pojemności na niskim.

W ten sposób można poprawić wydajność w okresach, w których konieczne jest większe skalowanie w celu zwiększenia wykorzystania zasobów, a także zmniejszyć koszty w okresach, w których wykorzystuje się mniej zasobów.

Kiedy tworzysz zaplanowaną akcję, określasz:

- [Scalable target](#Scalable%20target),
- kiedy powinna wystąpić czynność skalowania,
- minimalną i maksymalną pojemność.

# Skalowanie zasobów

## Aurora

#database

[źródło](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Integrating.AutoScaling.html#Aurora.Integrating.AutoScaling.AddConsole)

[Aurora](Aurora.md) skaluje się poprzez zdefiniowanie i zastoswanie [Scaling policy](#Scaling%20policy) do jej [klastra DB](Aurora.md#Aurora%20DB%20Cluster). Polityka skalowania definiuje minimalną i maksymalną liczbę Replik [Aurora](Aurora.md), którymi może zarządzać **Aurora Auto Scaling**.

Aby skorzystać z funkcji automatycznego skalowania Aurora, aplikacje muszą obsługiwać połączenia z nowymi replikami Aurora. Aurora Auto Scaling skaluje [klaster DB](Aurora.md#Aurora%20DB%20Cluster) tylko wtedy, gdy wszystkie repliki Aurora w klastrze DB są w stanie `available`. Jeśli którakolwiek replika jest w stanie innym niż `available`, Aurora Auto Scaling czeka aż cały klaster DB stanie się dostępny do skalowania.

[Globalne bazy danych](Aurora.md#Globalna%20baza%20danych) nie obsługują obecnie automatycznego skalowania dla drugorzędnych [klastrów DB](Aurora.md#Aurora%20DB%20Cluster).

> [Service-linked role](IAM.md#Service-linked%20role) `AWSServiceRoleForApplicationAutoScaling_RDSCluster` jest automatycznie tworzona przez Aurora  podczas rejestrowania zasobów Aurora jako [Scalable target](#Scalable%20target)s. Ta rola umożliwia **Application Auto Scaling** wykonywanie obsługiwanych operacji w ramach konta.

### Polityki skalowania

Aurora Auto Scaling wykorzystuje politykę skalowania do dostosowania liczby replik [Aurora](Aurora.md) w [klastrze DB](Aurora.md#Aurora%20DB%20Cluster).
Składa się z następujących komponentów:

- [Service-linked role](IAM.md#Service-linked%20role) - `AWSServiceRoleForApplicationAutoScaling_RDSCluster`;
- Metryki celu:
  - średnie użycie CPU - `RDSReaderAverageCPUUtilization`,
  - srednia połoczeń do bazy danych - `RDSReaderAverageDatabaseConnections`;
- Minimalna i maksymalna pojemność:
  - liczba replik - 0-15,
  - min <= max;
- [Cooldown](#Cooldown) (w sekundach):
  - dla aktywności scale-in i scale-out,
  - domyślnie 300s.

Zablokowanie aktywnoći **scale-in** chroni przez usunięciem replik [Aurora](Aurora.md).
Aktywność **scale-out** jest odblokowanie, aby móc tworzyć nowe repliki wedle zapotrzebowania.

---

# See also

- [EC2 Auto Scaling](EC2%20Auto%20Scaling.md)
