#AWS #storage

[źródło](https://docs.aws.amazon.com/efs/latest/ug/whatisefs.html)

Elastic File System (**EFS**) to prosty, bezserwerowy, elastyczny system plików typu "set-and-forget" do użytku z usługami w chmurze AWS i zasobami lokalnymi. Jest to w pełni zarządzany system plików **NFS** (network file system), który został zaprojektowany specjalnie dla obciążeń Linux (bazuje na interfejsie [POSIX](https://pl.wikipedia.org/wiki/POSIX)) dla wielu [EC2](EC2.md) jednocześnie. Umożliwia również przechowywanie danych w wielu [AZ](AWS%20locations.md#Availability%20zone), dzięki czemu domyślnie uzyskuje się pewną trwałość. Obsługuje skalowanie do petabajtów danych.

Dostępna jest opcja szyfrowania w spoczynky używająca [Key Management Service](Key%20Management%20Service.md).

EFS jest znacznie droższy od [Elastic Block Store](Elastic%20Block%20Store.md), ale płaci się faktyczne użycie, do którego  EFS się skaluje.

##### Spis treści

- [EFS storage classes](#EFS%20storage%20classes)
  - [Standard vs One Zone](#Standard%20vs%20One%20Zone)
  - [Infrequent-Access](#Infrequent-Access)
- [Tryby EFS](#Tryby%20EFS)
- [See also](#See%20also)

# EFS storage classes

[źródło](https://docs.aws.amazon.com/efs/latest/ug/storage-classes.html)

**EFS** oferuje szereg klas pamięci masowej, które są przeznaczone dla różnych przypadków użycia.

Należą do nich:

- EFS Standard,
- EFS Standard-Infrequent Access (EFS Standard-IA),
- EFS One Zone,
- EFS One Zone-Infrequent Access (EFS One Zone-IA).

## Standard vs One Zone

- **EFS Standard**,
- **EFS Standard-IA:**

Klasy przechowywania **EFS Standard** i **EFS Standard-IA** są [regionalnymi](AWS%20locations.md#Region) klasami przechowywania, które zostały zaprojektowane w celu zapewnienia ciągłej dostępności danych i metadanych redundantine w wielu [AZ](AWS%20locations.md#Availability%20zone). w ramach [regionu](AWS%20locations.md#Region).

> Klasa pamięci masowej Standard jest używana do często używanych plików. Jest to klasa pamięci masowej, w której początkowo zapisywane są dane klientów w przypadku standardowych klas pamięci masowej.

- **EFS One Zone**,
- **EFS One Zone-IA:**

Opcją na redukjce kosztów jest użycie **EFS One Zone** oraz **EFS One Zone-Infrequent Access**.

Te klasy pamięci zapewniają ciągłą dostępność danych i metadanych, przechowując je redundantnie w ramach pojedynczej [AZ](AWS%20locations.md#Availability%20zone). Z tego powodu dane mogą zostać utracone w przypadku awarii [AZ](AWS%20locations.md#Availability%20zone).

Aby zapewnić dodatkową ochronę danych, **Amazon EFS** automatycznie tworzy kopie zapasowe systemów plików przy klas **One Zone** za pomocą [AWS Backup](AWS%20Backup.md). Kopie zapasowe EFS zarządzane przez [AWS Backup](AWS%20Backup.md), są replikowane do trzech [AZ](AWS%20locations.md#Availability%20zone).

> EFS One Zone-Standard jest używany do często używanych plików. Jest to klasa pamięci masowej, w której początkowo zapisywane są dane klienta dla klas pamięci masowej One Zone.

## Infrequent-Access

- **EFS Standard-IA**,
- **EFS One Zone-IA:**

Istnieje także możliwość redukcji kosztów poprzez automatyczne przenoszenia danych do tańszej warstwy nieczęstego dostępu (**Infrequent-Access**). Klasy **IA** zmniejszają koszty pamięci masowej dla plików, do których nie potrzeba codziennego dostępu. Klasy **IA** są kompatybilne ze wszystkimi funkcjami **Amazon EFS**.

> Przykłady obejmują utrzymywanie dostępności plików w celu spełnienia wymogów audytu, przeprowadzanie analiz historycznych lub wykonywanie kopii zapasowych i odzyskiwanie danych.

# Tryby EFS

EFS został zaprojektowany, aby zapewnić przepustowość, IOPS i niskie opóźnienia potrzebne do szerokiego zakresu obciążeń. Dostępe po 2 tryby wydajności i przepustowości:

- **Performance mode**:

  - **General Purpose** - domyślny - Iidealny dla przypadków użycia wrażliwych na opóźnienia, takich jak środowiska serwowania stron internetowych, systemy zarządzania treścią, katalogi domowe i ogólne serwowanie plików;

  - **Max I/O** - Systemy plików w trybie Max I/O mogą skalować się do wyższych poziomów łącznej przepustowości i operacji na sekundę z kompromisem w postaci nieco wyższych opóźnień dla operacji na metadanych plików;

- **Throughput mode**:

  - **Bursting** - domyślny - przepustowość skaluje się wraz ze wzrostem systemu plików;

  - **Provisioned** - można określić przepustowość systemu plików niezależnie od ilości przechowywanych danych.

---

# See also

- [Elastic Block Store](Elastic%20Block%20Store.md)
- [EC2 Instance Store](EC2%20Instance%20Store.md)
