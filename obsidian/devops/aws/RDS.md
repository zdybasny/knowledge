#AWS #database

**Relational Database Service** to w pełni zarządzane serwisy dla relacyjnych baz danych.

RDS oferuje:

- automatyczne zaopatrzenia (provisioning) i patchowanie,
- kopie bezpieczeństwa baz danych i ich przywracanie,
- monitoring z dashboradami,
- repliki tylko do odczytu w celu poprawienia wydajności,
- wsparcie dla [Multi-AZ](#Multi-AZ%20deployment) i Disaster Recovery (**DR**),
- [skalowanie](Auto%20Scaling.md) baz danych (horyzontalne i wertykalne).

Dostępne platformy RDS:

- MySQL,
- PostgresSQL,
- MariaDB,
- Oracle Database,
- SQL Server,
- Amazon [Aurora](Aurora.md).

##### Spis treści

- [Instancja bazy danych](#Instancja%20bazy%20danych)
  - [Pamięć masowa dla instancji](#Pamięć%20masowa%20dla%20instancji)
- [Backup](#Backup)
- [Autoskalowanie](#Autoskalowanie)
- [Read Replica](#Read%20Replica)
  - [Replikacja do innych AZ i Regionów](#Replikacja%20do%20innych%20AZ%20i%20Regionów)
  - [Disaster Recovery i promocja repliki odczytu](#Disaster%20Recovery%20i%20promocja%20repliki%20odczytu)
- [Multi-AZ deployment](#Multi-AZ%20deployment)
  - [Multi-AZ DB instance deployment](#Multi-AZ%20DB%20instance%20deployment)
  - [Multi-AZ DB cluster deployment](#Multi-AZ%20DB%20cluster%20deployment)
- [Security](#Security)
  - [Encryption](#Encryption)
  - [IAM Database Authentication](#IAM%20Database%20Authentication)
  - [Database Activity Streams](#Database%20Activity%20Streams)
- [DMS – Database Migration Service](#DMS%20–%20Database%20Migration%20Service)
- [See also](#See%20also)

# Instancja bazy danych

[źródło](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)

Instancja DB jest odizolowanym środowiskiem bazy danych w chmurze AWS.

Instancja DB może zawierać jedną lub więcej baz danych utworzonych przez użytkownika.

Dostęp do instancji DB można uzyskać za pomocą tych samych narzędzi i aplikacji, które są używane z samodzielną instancją bazy danych. Instancję DB można tworzyć i modyfikować za pomocą:

- AWS CLI,
- API RDS,
- lub konsoli AWS.

Istnieje możliwość uruchamiania instancji DB wewnątrz [VPC](VPC.md) w celu izolacji i zapewnienia kontroli nad środowiskiem sieciowym.

RDS używa **Network Time Protocol** (NTP) do synchronizacji czasu na instancjach DB.

## Pamięć masowa dla instancji

[źródło](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Storage.html)

RDS oferuje trzy rodzaje pamięci masowej:

- [General purpose SSB - gp](Elastic%20Block%20Store.md#General%20purpose%20SSB%20-%20gp),
- [Provisioned IOPS SSD - io](Elastic%20Block%20Store.md#Provisioned%20IOPS%20SSD%20-%20io),
- [magnetyczny (HDD) - st](Elastic%20Block%20Store.md#Throughput%20Optimizad%20HDD%20-%20st).

Różnią się one wydajnością i ceną, co oznacza, że można dostosować wydajność i koszt pamięci masowej do potrzeb danego obciążenia bazy danych.

# Backup

[źródło](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html) [źródło](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PIT.html)

RDS tworzy i zapisuje zautomatyzowane kopie zapasowe instancji DB podczas okna backupu. Funkcjonalność ta jest domyślnie włączona. Kopię zapasową instancji DB można także utworzyć ręcznie.

RDS tworzy snapshot całej instancji DB, a nie tylko poszczególnych baz danych. RDS zapisuje zautomatyzowane kopie zapasowe instancji DB zgodnie z okresem przechowywania kopii zapasowych określonym przez użytkownika (domyślnie 7, max 35 dni).

Pierwszy snapshot instancji DB zawiera dane dla całej instancji DB. Kolejne zrzuty tej samej instancji DB są przyrostowe, co oznacza, że zapisywane są tylko te dane, które uległy zmianie po ostatnim zrzucie. Logi transacji są zapisywane do [S3](S3.md) co 5 minut.

> Aby zobaczyć najnowszy czas restorowalny dla instancji DB:
>
> 1. użyj polecenia: `aws rds describe-db-instances`
>
> i spójrz na wartość zwróconą w polu `LatestRestorableTime` dla instancji DB
>
> 2. lub w konsoli `RDS > Automaded backups`.

Jeśli to konieczne, możesz odzyskać bazę danych do dowolnego punktu w czasie okresu przechowywania kopii zapasowej, tworząc nową instancję DB. Można wybrać domyślną [Security group](Security%20group.md) dla [VPC](VPC.md) lub zastosować niestandardową do instancji DB.

# Autoskalowanie

#autoscaling

[RDS Storage Auto Scaling](RDS%20Storage%20Auto%20Scaling.md) stale monitoruje rzeczywiste zużycie pamięci masowej dla **RDS** i skaluje wertykalnie pojemność automatycznie, gdy rzeczywiste wykorzystanie zbliża się do dostarczonej pojemności pamięci masowej.

# Read Replica

[źródło](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html)

**RDS** używa wbudowane w silniki bazodanowe funckjonalności replikacji, aby tworzyć specjalne instancje DB zwane repliką odczytu (**read replica**). Z tego też powodu repliki te różnią się w zależności od zastowaniego silnika DB.

[Aurora](Aurora.md) może posiadać 15 replik na klaster, natomiast pozostałe silniki RDS tylko 5. 

Źródłowa instancja DB staje się podstawową instancją DB (**primary DB instance**). Aktualizacje na niej wykonywane są **asynchronicznie** kopiowane do repliki odczytu. Można zmniejszyć obciążenie podstawowej instancji DB poprzez kierowanie zapytań odczytu z aplikacji do repliki odczytu.

Aby zacząć replikować instancję, która już świadczy usługi, wystarczy ją włączyć. Nowa replika odczytu jest tworzona przez RDS przy użyciu snapshota. Przy tej operacji nie ma żadnego przestoju w instancji podstawowej.

Repliki odczytu dodają nowe endpointy z własną nazwą [DNS](../Network/DNS.md). Musimy zatem zmienić naszą aplikację tak, aby odwoływała się do nich indywidualnie, aby zrównoważyć obciążenie odczytem.

![](attachments/RDS%20Read%20Replica.png)

Korzystając z replik odczytu, można elastycznie skalować horyzontalnie poza ograniczenia pojemności pojedynczej instancji DB w przypadku obciążeń bazodanowych o dużym natężeniu odczytu.

## Replikacja do innych AZ i Regionów

W niektórych przypadkach replika odczytu rezyduje w innym [regionie AWS](AWS%20locations.md#Region) niż jej podstawowa instancja DB. W takich przypadkach **RDS** tworzy bezpieczny kanał komunikacyjny pomiędzy główną instancją DB a repliką odczytu. **RDS** ustanawia wszelkie konfiguracje bezpieczeństwa AWS potrzebne do uruchomienia bezpiecznego kanału, takie jak dodawanie wpisów grup bezpieczeństwa.

Multi-AZ zachowuje ten sam connection string niezależnie od tego, jak baza danych jest skonfigurowana.

Można skonfigurować replikę odczytu dla instancji DB, która ma również replikę rezerwową skonfigurowaną w celu zapewnienia wysokiej dostępności we [wdrożeniu typu Multi-AZ](#Multi-AZ%20deployment). Replikacja z repliką rezerwową (**standby**) jest synchroniczna, a replika rezerwowa nie może obsługiwać ruchu odczytu.

Amazon nalicza koszty podczas transferu danych pomiędzy [AZ](AWS%20locations.md#Availability%20zone), jednak dla replik odczytu w tym samym regionie takie koszty nie są naliczane.

![](attachments/RDS%20Read%20with%20Standby%20replicas.png)

## Disaster Recovery i promocja repliki odczytu

Replikę odczytu można wypromować na samodzielną instancję DB. Gdy promujesz replikę odczytu, instancja DB jest restartowana zanim stanie się dostępna. Można to wykożystać do przywrócania bazy po awari (**Disaster Recovery**).

![](attachments/RDS%20rr%20promotion.png)

# Multi-AZ deployment

[źródło](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html)

Wdrożenia typu multi-AZ mogą mieć jedną rezerwową lub dwie rezerwowe instancje DB.

Jeśli wdrożenie ma jedną rezerwową instancję DB, nazywa się ono Multi-AZ DB **instance** deployment.
Gdy wdrożenie ma dwie rezerwowe instancje DB, jest nazywane Multi-AZ DB **cluster** deployment.

## Multi-AZ DB instance deployment

**RDS** automatycznie tworzy i utrzymuje synchroniczną replikę rezerwową w innej [AZ](AWS%20locations.md#Availability%20zone). Podstawowa instancja DB jest synchronicznie replikowana pomiędzy strefami dostępności do repliki rezerwowej, aby zapewnić redundancję danych i zminimalizować skoki opóźnień podczas tworzenia kopii zapasowych systemu.

Rezerwowa instancja DB zapewnia obsługę przełączania awaryjnego, ale nie obsługuje ruchu odczytu.

Uruchamianie instancji DB z wysoką dostępnością może zwiększyć dostępność podczas planowanej konserwacji systemu. Może również pomóc chronić bazy danych przed awarią instancji DB i zakłóceniami w strefach dostępności.

> Wysokiej dostępność nie jest rozwiązaniem skalowania dla scenariuszy read-only. Nie możesz użyć repliki standby do obsługi ruchu read-only. Aby obsługiwać ruch tylko do odczytu, należy użyć [Multi-AZ DB cluster deployment](#Multi-AZ%20DB%20cluster%20deployment) lub [Read Replica](#Read%20Replica).

![](attachments/RDS%20Multi-AZ%20instance.png)

## Multi-AZ DB cluster deployment

RDS replikuje dane z instancji writer DB do obu instancji reader DB wykorzystując natywne możliwości replikacji silnika DB. Gdy zmiana jest dokonywana na instancji writer DB, jest ona wysyłana do każdej instancji reader DB. Potwierdzenie z co najmniej jednej instancji reader DB jest wymagane, aby zmiana została zatwierdzona i zastosowana.

Instancje  DB typu reader działają jako automatyczne cele awaryjne i obsługują również ruch odczytu w celu zwiększenia przepustowości odczytu aplikacji. Jeśli nastąpi awaria instancji writer DB, RDS zarządza, która z instancji reader DB stanie się celem awaryjnym. RDS robi to w oparciu o to, która instancja reader DB ma najmniejsze opóźnienie repliki.

Wdrożenie klastra DB Multi-AZ ma rezerwowe instancje DB, które zapewniają obsługę przełączania awaryjnego i mogą obsługiwać ruch odczytu.

![](attachments/RDS%20Multi-AZ%20cluster.png)

# Security

[źródło](https://aws.amazon.com/rds/features/security/)

RDS oferuje zestaw funkcji zapewniających bezpieczne przechowywanie i dostęp do danych.

- Uruchom swoją bazę danych w [VPC](VPC.md), aby uzyskać izolację na poziomie sieci.

- Użyj [Security group](Security%20group.md)s, aby kontrolować, które adresy IP lub [EC2](EC2.md) mogą łączyć się z bazą danych. Wbudowany firewall uniemożliwia dostęp do bazy danych za wyjątkiem reguł, które określisz.

- Użyj polityk [IAM](IAM.md), aby przypisać uprawnienia, które określają, kto może zarządzać zasobami RDS.

- Użyj zabezpieczeń silnika bazy danych, aby kontrolować, kto może zalogować się do baz danych, tak jak w przypadku, gdy baza danych znajduje się w sieci lokalnej. Można również mapować użytkowników bazy danych do ról IAM dla federacyjnego dostępu. (MySQL i PostgreQSL)

- Używaj połączeń SSL/TLS, aby zaszyfrować dane podczas ich przesyłania.

- Szyfruj przechowywanie bazy danych i kopie zapasowe w spoczynku algorytmem AES-256 za pomocą usługi [Key Management Service](Key%20Management%20Service.md) (KMS).

- Dzięki **Transparent Data Encruption** (**TDE**) serwer DB automatycznie szyfruje dane przed zapisem i odszyfrowuje podczas ich odczytu. TDE dostępny jest dla Oracle i SQL Server.

- Monitorowanie aktywności bazy danych i integracja z partnerskimi aplikacjami bezpieczeństwa baz danych za pomocą **Database Activity Streams**.

## Encryption

Aby zaszyfrować DB, trzeba wybrać tę opcję podczas jej tworzenia. Snapshoty są szyfrowane, jeśli tylko instancja DB jest szyfrowania. Aby zaszyfrować snapshot z nieszyfrowanej instancji, należy wybrać taką opcję podczas kopiowania snapshotów. Wtedy teżmożna przywrócić DB, tworząc nową (już szyfrowaną) instancję.

## IAM Database Authentication

RDS jest zintegrowany z [IAM](IAM.md) i zapewnia możliwość kontroli działań, które [użytkownicy](IAM.md#IAM%20User), [grupy IAM](IAM.md#IAM%20User%20Group) i inne zasoby z przypisanymi [rolami](IAM.md#IAM%20Role) mogą podejmować na określonych zasobach. Dodatkowo, można tagować swoje zasoby Amazon RDS i kontrolować działania, które użytkownicy IAM i grupy mogą podjąć na grupach zasobów, które mają ten sam tag i powiązaną wartość.

IAM Database Authentication jest dostępne dla MySQL i PostgreSQL, natomiast  nie jest wspierane przez Oracle. 

Podczas pierwszego tworzenia instancji DB w ramach Amazon RDS, utworzone zostanie główne konto użytkownika, które jest używane tylko w kontekście Amazon RDS do kontroli dostępu do instancji DB. Główne konto użytkownika umożliwia logowanie się do instancji DB ze wszystkimi uprawnieniami bazy danych. Po utworzeniu instancji DB można łączyć się z bazą danych przy użyciu poświadczeń użytkownika głównego. Następnie można utworzyć dodatkowe konta użytkowników, aby ograniczyć dostęp do instancji DB.


## Database Activity Streams

Zarządzane bazy danych muszą też zapewniać ochronę przed zagrożeniami wewnętrznymi ze strony administratorów baz danych (DBA). **Database Activity Streams** dostarcza strumień danych w czasie rzeczywistym o aktywności bazy danych w relacyjnej bazie danych. Po zintegrowaniu z zewnętrznymi narzędziami do monitorowania aktywności bazy danych, można monitorować i audytować aktywność bazy danych w celu zapewnienia ochrony bazy danych i spełnienia wymogów zgodności i regulacji.

Obecnie **Database Activity Streams** obsługiwany przez [Aurora](Aurora.md) i Amazon RDS dla Oracle.

![](attachments/DB%20Activity%20Streams.png)

# DMS – Database Migration Service

Umożliwia migrację danych do AWS z już istniejących baz danych. Wspiera migrację danych jednorazową (one-time) oraz ciągłą (continual). Wspiera wiele popularnych baz danych, zarówno komercjalnych jak i open-source.

Płaci się tylko za moc obliczeniową zużywaną podczas migracji.

---

# See also

- [Aurora](Aurora.md)
- [RDS Storage Auto Scaling](RDS%20Storage%20Auto%20Scaling.md)
