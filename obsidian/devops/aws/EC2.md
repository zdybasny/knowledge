#AWS #compute

Amazon Elastic Compute Cloud (Amazon EC2) oferuje platformę obliczeniową, z instancjami i wyborem  procesora, pamięci masowej, sieci, systemu operacyjnego i modelu zakupu.

##### Spis treści

- [Typy instancji](#Typy%20instancji)
  - [Ogólnego zastosowania - t, m](#Ogólnego%20zastosowania%20-%20t,%20m)
  - [Zoptymalizowana pod kątem obliczeń (compute) - c](#Zoptymalizowana%20pod%20kątem%20obliczeń%20(compute)%20-%20c)
  - [Zoptymalizowana pod kątem pamięci (memory) - r, x, z](#Zoptymalizowana%20pod%20kątem%20pamięci%20(memory)%20-%20r,%20x,%20z)
  - [Zoptymalizowana pod kątem pamięci masowej (storage) - i, d, h](#Zoptymalizowana%20pod%20kątem%20pamięci%20masowej%20(storage)%20-%20i,%20d,%20h)
  - [Ze wsparciem sprzętowym](#Ze%20wsparciem%20sprzętowym)
- [Opcje kupna](#Opcje%20kupna)
  - [on-demand](#on-demand)
  - [Reserved Instances](#Reserved%20Instances)
    - [Standard](#Standard)
    - [Convertible](#Convertible)
    - [Scheduled](#Scheduled)
  - [Savings Plan](#Savings%20Plan)
  - [Spot](#Spot)
    - [Spot Fleets](#Spot%20Fleets)
  - [Dedicated Hosts](#Dedicated%20Hosts)
    - [AWS License Manager](#AWS%20License%20Manager)
    - [Deticated Instances vs Deticated Hosts](#Deticated%20Instances%20vs%20Deticated%20Hosts)
- [Placement Group](#Placement%20Group)
- [EC2 User Data](#EC2%20User%20Data)
- [Storage](#Storage)
- [EC2 Auto Scaling](#EC2%20Auto%20Scaling)
  - [Auto Scaling group](#Auto%20Scaling%20group)
- [See also](#See%20also)

# Typy instancji

Typy instancji Amazon EC2 są zoptymalizowane do różnych zadań. Wybierając typ instancji, weź pod uwagę specyficzne potrzeby Twoich obciążeń i aplikacji.
Może to obejmować wymagania dotyczące:

- mocy obliczeniowej,
- pamięci,
- możliwości przechowywania danych.

**Typy instancji:**

## Ogólnego zastosowania - t, m

Ddostarcza zbalansowane zasoby (CPU, RAM, dysk, sieciowe) przeznaczona dla aplikacji, które nie potrzebują optymalizacji pod kątem żadnego konretnego zasobu
Zastosowanie:

- serwery aplikacji (w tym backend),
- serwery do gier,
- małe i średnie bazy danych.

## Zoptymalizowana pod kątem obliczeń (compute) - c

Dostarczają wysoko wydajne procesory.
Zastosowanie:

- aplikacje i serwery, które wymagają wiele obliczeń lub przetwarzania wielu transakcji.

## Zoptymalizowana pod kątem pamięci (memory) - r, x, z

Zapewnia szybką wydajność dla obciążeń, które przetwarzają duże zbiory danych w pamięci.
Zastosowanie:

- obciążenia wygmagające załadowania dużej ilości danych przed uruchomieniem lub w trakcie działania plikacji,
- bazy danych przetwarzające duże ilości danych w czasie rzeczywistym.

## Zoptymalizowana pod kątem pamięci masowej (storage) - i, d, h

Zastosowanie:

- obciążenia wymagające wysokiego, sekwencyjnego dostępu i/o dużych zbiorów danych (_IOPS_ - input output per second)
- duże bazy danych,
- rozproszone systemy plików,
- hurtownie danych,
- systemy przetwarzania transakcji online (_OLTP_) o wysokiej częstotliwości.

## Ze wsparciem sprzętowym

Wykorzystują akceleratory sprzętowe lub koprocesory do wykonywania niektórych funkcji w sposób bardziej efektywny niż jest to możliwe w oprogramowaniu działającym na procesorach. Przykłady takich funkcji obejmują obliczenia na liczbach zmiennoprzecinkowych, przetwarzanie grafiki i dopasowywanie wzorców danych.
Zastosowanie:

- aplikacje graficzne,
- strumieniowe przesyłanie gier
- strumieniowe przesyłanie aplikacji

# Opcje kupna

## on-demand

**(Domyśłna)**
Instancje na żądanie pozwalają Ci płacić za moc obliczeniową za godzinę lub sekundę (minimum 60 sekund) bez żadnych długoterminowych zobowiązań. Uwalnia Cię to od kosztów i zawiłości związanych z planowaniem, zakupem i utrzymaniem sprzętu, a także przekształca zwykle duże koszty stałe w znacznie mniejsze koszty zmienne.

## Reserved Instances

Zapewnia zniżki w stosunku do modelu On-Demand, gdy możesz zobowiązać się do określonego okresu (1 lub 3 lata).
Dodatkowo, gwarantuje on również rezerwację pojemności dla określonego typu instancji. na cały wykupiony okres.
Można opłacać:

- całość z góry (_all upfront_)
- częsciowo z góry (_partial upfront_)
- miesięcznie (_no upront_)

Dostępne są 3 plany rezerwacji instancji:

- Standard
- Convertible
- Scheduled

### Standard

Daje najwyższy rabat i to działa dobrze tylko dla stałych obciążeń roboczych. Oznacza to, że nie można zmienić instancji w ciągu zamówionego okresu.

### Convertible

Pozwala na konwersję niektórych atrybutów, jeśli będzie to miało taką samą wartość. Jest to również świetne rozwiązanie dla stabilnych obciążeń.

### Scheduled

Scheduled Reserved Instance opłaca się w przypadku obciążeń zmiennych, ale przewidywalnych, np. wzmożony ruch w okresie świątecznym lub w określonych godzinach.

## Savings Plan

Podobny do instancji zarezerwowanych, z tą różnicą, że nie ogranicza się tylko do EC2. Obsługuje również [Fargate](Fargate.md) i [Lambda](Lambda.md). W przeciwieństwie do Reserved Instances nie rezerwuje pojemności.

## Spot

Pozwala na wykorzystanie nadmiaru mocy obliczeniowej EC2, które mogą istnieć w [Availability zone](AWS%20locations.md#Availability%20zone). Pozwala uzyskać nawet 90% zniżki. Istnieje cena rynkowa dla typów instancji na strefę dostępności i jest to tzw. **cena spot** (spot prise). Jeśli twoja oferta jest wyższa niż cena spot, będziesz mógł uruchomić swoje instancje. Jeśli cena spot przekroczy twoją ofertę, wtedy twoja instancja zostanie zamknięta w ciągu 2 minut. Z tego powodu nadaje się w przypadku obciążeń, które mogą się uruchamiać i zamykać bez negatywnego wpływu na system.

Nadaje szczególnie do:

- bartch jobs,
- rozproszonych obciążeń,
- obciążeń z elastycznym czasem startu i końca,
- analizy danych,
- przetwarzanie obrazu.

**Nie** nadaje się do:

- krytycznych zadań,
- baz danych.

### Spot Fleets

#ENG
The Spot Fleet will try to meet the target capacity with price constraints

- Define possible launch pools: instance type (m5.large), OS, Availability Zone
- Can have multiple launch pools, so that the fleet can choose
- Spot Fleet stops launching instances when reaching capacity or max cost

Strategies to allocate Spot Instances:

- lowestPrice: from the pool with the lowest price (cost optimization, short workload)
- diversified: distributed across all pools (great for availability, long workloads)
- capacityOptimized: pool with the optimal capacity for the number of instances

## Dedicated Hosts

Dostarcza dedykowany serwer fizyczny w centrum danych (data centre). Jest to najdroższa opcja. Przykładowym przypadkiem jego zapotrzebowania jest pewność przestrzegania warunków licencji per-serwer, per-core, per-VM, per-cocket.

### AWS License Manager

Dedicated Host jest zintegrowany z **AWS License Manager**.

![AWS License Manager.png](../attachments/AWS%20License%20Manager.png)

### Deticated Instances vs Deticated Hosts

#ENG
You can use Dedicated Hosts and Dedicated instances to launch Amazon EC2 instances on physical servers that are dedicated for your use. An important difference between a Dedicated Host and a Dedicated instance is that a Dedicated Host gives you additional visibility and control over how instances are placed on a physical server, and you can consistently deploy your instances to the same physical server over time. As a result, Dedicated Hosts enable you to use your existing server-bound software licenses and address corporate compliance and regulatory requirements.

![EC2 Dedicated instances vs host.png](../attachments/EC2%20Dedicated%20instances%20vs%20host.png)

# Placement Group

[źródło](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html)

Placement group pozwalają na określenie strategi rozmieszczenia EC2. Dostępne są następujące strategie:

- **Cluster**  - grupuje EC2 razem w jednej [AZ](AWS%20locations.md#Availability%20zone).
  - Zastosowane:
    - uzyskanie wysokiej wydajności sieci,
    - bazy danych.

- **Spread** - rozmieszcza każdą EC2 w osobnych fizycznych urządzeniach, które posiadają własną sieć i źródło zasilania. Max. 7 EC2 na [AZ](AWS%20locations.md#Availability%20zone)
  - Zastosowane:
    - zapewnienie izolacji dla krytycznych obciążeń,
    - aplikacje wymagające bardzo wysokiej dostępności.

- **Partition** - rozmieszcza EC2 w osobnych partycjach, ktore które bazują na osobnych urządzeniach wewnątrz [AZ](AWS%20locations.md#Availability%20zone).
  - Zastosowanie:
    - w dużych rozproszonych i powtarzalnych obciążeń, np. Hadoop, Cassandra, Kafka.

-

# EC2 User Data

EC2 User Data uruchamia zdefiniowany skrypt tylko raz, kiedy EC2 się uruchamia pierwszy raz.
Przykładowe zadania, które można tak wykonać:

- instalacja aktualizacja software'u
- pobranie kodu lub plików
- cokolwiek

Skrypt ten uruchamiany jest przez użytkownika root.

# Storage

#storage

Podczas tworzenia EC2 przypinany jest do nie volumen pamięci masowej typu [Elastic Block Store](Elastic%20Block%20Store.md) o nazwie _Root_. Domyślnie jest on ustawiony z atrybutem _Delete on Termination_. Można przypiąć do EC2 dodatkowe valuminy, które domyślnie nie usuwają się z końcem cyklu życia danej instancji.

![EC2 EBS.png](../attachments/EC2%20EBS.png)

Dostępne typy voluminów dla EC2 to:

- [EBS](Elastic%20Block%20Store.md)
- [Instance Store](EC2%20Instance%20Store.md)
- ...

# EC2 Auto Scaling

#autoscaling

[EC2 Auto Scaling](EC2%20Auto%20Scaling.md) umożliwia automatyczne dodawanie lub usuwanie instancji Amazon EC2 w ramach [Auto Scaling Group](EC2%20Auto%20Scaling.md#Auto%20Scaling%20Group) w odpowiedzi na zmieniające się zapotrzebowanie aplikacji.

**Więcej: [EC2 Auto Scaling](EC2%20Auto%20Scaling.md)**

---

# See also

- [Security group](Security%20group.md)
- [Elastic IP](Elastic%20IP.md)
- [Network ACL](Network%20ACL.md)
- [NAT devices](NAT%20devices.md)
- [Elastic Block Store](Elastic%20Block%20Store.md)
- [EC2 Auto Scaling](EC2%20Auto%20Scaling.md)
