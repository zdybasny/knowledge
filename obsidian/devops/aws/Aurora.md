#AWS #database

**Aurora** jest relacyjną bazą danych kompatybilną z MySQL i PostgreSQL, która została stworzona do zastosowań chmurowych. Łączy wydajność i dostępność baz danych typu enterprise z prostotą i efektywnością kosztową baz danych open-source. Zawiera wysokowydajny podsystem pamięci masowej.

Aurora jest częścią [RDS](RDS.md).

##### Spis treści

- [Aurora a RDS](#Aurora%20a%20RDS)
- [Aurora High Availability](#Aurora%20High%20Availability)
  - [Aurora DB Cluster](#Aurora%20DB%20Cluster)
  - [Endpointy klastra](#Endpointy%20klastra)
  - [Przywracanie po awari](#Przywracanie%20po%20awari)
  - [Globalna baza danych](#Globalna%20baza%20danych)
- [Autoskalowanie](#Autoskalowanie)
- [Security](#Security)
- [Aurora Serverless](#Aurora%20Serverless)
- [Multi-master cluster](#Multi-master%20cluster)
- [See also](#See%20also)

# Aurora a RDS

Poniższe punkty ilustrują, w jaki sposób Aurora odnosi się do standardowych silników MySQL oraz PostgreSQL dostępnych w [RDS](RDS.md):

- Aurora widjnieje jako silnik DB podczas konfigurowania nowych serwerów baz danych poprzez [RDS](RDS.md).

- Aurora wykorzystuje funkcje [RDS](RDS.md) do zarządzania i administracji, takich jak:
  - provisioning,
  - patchowanie,
  - tworzenie kopii zapasowych,
  - odzyskiwanie DB,
  - wykrywanie awarii i naprawa.

- Aurora zazwyczaj wykonuje operacje na całych klastrach synchronizowancyh poprzez replikację, zamiast pojedynczych instancji baz danych. Automatyczne tworzenie klastrów, replikacja oraz alokacja pamięci masowej sprawiają, że tworzenie, obsługa i skalowanie największych wdrożeń baz danych MySQL oraz PostgreSQL jest proste i opłacalne.

- Możesz przenieść dane z [RDS](RDS.md) dla MySQL i PostgreSQL do Aurory, tworząc i przywracając snapshoty lub ustawiając replikację jednokierunkową. Możesz użyć narzędzi do migracji, aby przekonwertować istniejące aplikacje na Aurorę.

- Aurora jest wydajniejsza od RDS, przez co kosztuje o 20% więcej.

# Aurora High Availability

Na wysoką dostępność Aurory składa się kilka czynników, takich jak klasterDB i jego volunmin, automatyczna replikacja pomiędzy [AZ](AWS%20locations.md#Availability%20zone), automatyczne przywracanie po awari.

## Aurora DB Cluster

[źródło](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.html)

Klaster DB Aurory składa się z jednej lub więcej [instancji DB](RDS.md#Instancja%20bazy%20danych) oraz **DB cluster volume**, który zarządza danymi tych instancji DB. Wolumen klastra jest wirtualnym wolumenem przechowywania bazy danych, który obejmuje wiele [AZ](AWS%20locations.md#Availability%20zone), przy czym każda [AZ](AWS%20locations.md#Availability%20zone) posiada kopię danych klastra DB. Klaster DB Aurora składa się z dwóch typów instancji DB:

- **Podstawowa instancja DB** - Obsługuje operacje odczytu i zapisu oraz wykonuje wszystkie modyfikacje danych na woluminie klastra. Każdy klaster ma jedną podstawową instancję DB.
- **Aurora Replica** - łączy się z tym samym woluminem pamięci masowej co podstawowa instancja DB i obsługuje tylko operacje odczytu. Każdy klaster może mieć do 15 dodatkowych replik, które są umieszczenie w oddzielnych [AZ](AWS%20locations.md#Availability%20zone) czy też nawet w oddzielnych [regionach](AWS%20locations.md#Region) ([Globalna baza danych](#Globalna%20baza%20danych)).

Każda [AZ](AWS%20locations.md#Availability%20zone) posiada własną kopię volimunu.

![Aurora Cluster.png](../attachments/Aurora%20Cluster.png)

## Endpointy klastra

Aurora dostarcza endpoint do zapisu danych na współdzielonym voluminie poprzez instancję podstawową oraz endpoint tylko do odczytu, który czyta dane z voliminu poprzez repliki Aurory. Pozwala podnieść wydajność całego klastra.

Istnieje możliwość definiowania własnych endpointów, poprzez które można odpytać wybrane repliki (np. te na wydajniejszych instancjach).

## Przywracanie po awari

Aurora automatycznie przełącza się na replikę Aurory w przypadku, gdy podstawowa instancja DB staje się niedostępna. Można określić priorytet przełączania awaryjnego dla replik Aurora. Repliki Aurora mogą również odciążać podstawową instancję DB od obciążeń związanych z odczytem.

## Globalna baza danych

Globalne bazy danych Aurora obejmują wiele [regionów AWS](AWS%20locations.md#Region"). Umożliwia globalne odczyty z małymi opóźnieniami i zapewnia szybkie odzyskiwanie danych w przypadku rzadkich przestojów, które mogą dotknąć cały Region.

Globalna baza danych ma główny klaster (zapis i odczyt) w jednym Regionie i do pięciu drugorzędnych klastrów (odczyt) w różnych Regionach.

Przywracanie po awarii trwa poniżej minuty dzięku promocji jednego z drugorzędnych klastrów.

# Autoskalowanie

[źródło](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Integrating.AutoScaling.html)

[Aurora Auto Scaling](Application%20Auto%20Scaling.md#Aurora) to funkcja automatycznego skalowania dynamicznie dostosowuje liczbę replik Aurora dostarczonych dla klastra, wykorzystującego replikację single-master, w uwzględnieniu obecnego obciążenia określonego za pomocą metryk [CloudWatch](CloudWatch.md) i wartości docelowych.

Aby użyć autoskalowanie klastra Aurora, należy go zarejestrowaćw [Application Auto Scaling](Application%20Auto%20Scaling.md) jako [Scalable target](Application%20Auto%20Scaling.md#Scalable%20target). Taka rejestracja ma miejsce automatycznie po skonfiguracji autoskalowania za pomocą konsoli Aurory.

# Security

[źródło](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/UsingWithRDS.html)

Kwestia bezpieczeństwa Aurory rozwiązana jest w podobny sposób do [bezpieczeństwa RDS](RDS.md#Security), gdyż używa tych smaych silnoków.

- Szyfrowanie w spoczynku z użyciem [Key Management Service](Key%20Management%20Service.md) dla:
  - instancji DB,
  - automatycznych backupów i snapshotów,
  - nowych replik;
- szyfrowanie w tranzycie używa SSL;
- [autentykacja IAM](RDS.md#IAM%20Authentication) z użyciem tokenu;
- należy dostarczyć [Security group](Security%20group.md);
- brak dostępo poprzez SSH

# Aurora Serverless

[źródło](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless.how-it-works.html)

**Aurora** oferuje dwa różne tryby silnika DB (przeznaczone do dwóch zasadniczo różnych modeli użycia):

- tryb **provisioned DB engine** - przeznaczony dla przewidywalnych obciążeń,
- tryb **serverless DB engine** - przeznaczony dla zupełnie niestandardowego wzorca użycia.

Dla przewidywalnych obciążeń można oszacować potrzebny rozmiar i klasę instancji DB oraz kilka innych opcji konfiguracyjnych w ramach modelu **provisioned DB engine**. Nawet jeśli obciążenie zmienia się, można zmodyfikować lub skalować rozmiar klasy instancji DB i zmienić liczbę replik Aurora. Jednak dla bardzo nieprzewidywalnych obciążeń (np. bardzo duże przez krótki okres czasu, a następnie długie okresy małej lub żadnej aktywności) zastowanie znajdzie **Aurora Serverless** w modelu **serverless DB engine**.

> Przykładem mogą być bazy danych tworzące raporty w razie potrzeby, środowiska programistyczne i testowe oraz nowe aplikacje o niepewnych wymaganiach. W przypadkach takich jak te i wielu innych, skonfigurowanie pojemności z wyprzedzeniem nie zawsze jest możliwe w modelu **provisioned DB**. Może to również skutkować wyższymi kosztami, jeśli przesadzisz z rezerwacją i będziesz mieć pojemność, której nie wykorzystasz.

Używając **Aurora Serverless v1,** możesz stworzyć endpoint bazy danych bez określania rozmiaru i klasy instancji DB. Określasz tylko minimalny i maksymalny zakres dla pojemności [Aurora DB Cluster](#Aurora%20DB%20Cluster) Aurora Serverless. Taki endpoint tworzy flotę routerów, która obsługuje ciągłe połączenia i dystrybuuje obciążenie pomiędzy zasobami. **Aurora Serverless** skaluje zasoby automatycznie w oparciu o Twoją minimalną i maksymalną specyfikację pojemności.

Nie musisz zmieniać kodu aplikacji klienta bazy danych, aby korzystać z floty routerów. Aurora Serverless v1 automatycznie zarządza połączeniami. Skalowanie jest szybkie dzięki "ciepłej" puli zasobów, która jest zawsze gotowa do obsługi żądań. Pamięć masowa i przetwarzanie są oddzielone, więc Twój klaster DB Aurora Serverless v1 może skalować się do zera, gdy skończy przetwarzać obciążenia. Gdy Twój klaster Aurora Serverless v1 DB skaluje się do zera, płacisz tylko za pamięć masową.

![Aurora Serverless.png](../attachments/Aurora%20Serverless.png)

# Multi-master cluster

[źródło](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-multi-master.html)

Dla każdego nowego klastra Amazon Aurora możesz wybrać, czy chcesz utworzyć klaster **single-master** czy **multi-master**.

W **klastrze multi-master** wszystkie instancje DB mają możliwość odczytu/zapisu. Klastry multi-master mają inną charakterystykę dostępności, wsparcie dla funkcji bazy danych oraz procedury monitorowania i rozwiązywania problemów niż klastry single-master.

Dostępnąć oferowana przez klaster multi-master nazywa się dostępnością ciągłą (**continuous availability**), aby odróżnić ją od wysokiej dostępności (**high availability**) (z krótkim czasem przestoju podczas przełączania awaryjnego) oferowanej przez klaster single-master.

Klaster multi-master **nie korzysta** z mechanizmu **failover**, ponieważ nie musi promować innej instancji DB, aby mieć możliwość odczytu/zapisu. W klastrze multi-master można badać metryki związane z przepustowością DML, opóźnieniami i blokadami dla wszystkich instancji DB, a nie dla pojedynczej instancji głównej.

---

# See also

- [RDS](RDS.md)
- [Aurora Auto Scaling](Application%20Auto%20Scaling.md#Aurora)
