#AWS #compute

[źródło](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html)

**Elastic Beanstalk** umożliwia szybkie wdrażanie aplikacje w chmurze AWS i zarządzanie nimi bez konieczności poznawania infrastruktury, na której działają te aplikacje. Wystarczy załadować aplikację, a Elastic Beanstalk automatycznie zajmie się szczegółami dostarczania, m.in:

- pojemności ([EC2](EC2.md)),
- środowiska uruchomieniowego (np. JRE, Python 3 RE)
- pamięci masowej ([Elastic Block Store](Elastic%20Block%20Store.md)),
- firewalla ([Security group](Security%20group.md)),
- równoważenia obciążenia ([Elastic Load Balancing](Elastic%20Load%20Balancing.md)),
- skalowania ([Auto Scaling](Auto%20Scaling.md)),
- monitorowania stanu aplikacji.

Nadal jednaj pozostaje możliwość konfiguracji [środowiska](#Environment) Beanstalk.

##### Spis treści

- [Komponenty i koncepcje](#Komponenty%20i%20koncepcje)
  - [Platform](#Platform)
    - [Obsługiwane platformy](#Obsługiwane%20platformy)
  - [Application](#Application)
  - [Application Version](#Application%20Version)
  - [Environment](#Environment)
  - [Environment Tier](#Environment%20Tier)
    - [Web server environment](#Web%20server%20environment)
    - [Worker environment](#Worker%20environment)
      - [Daemon SQS](#Daemon%20SQS)
  - [Environment configuration](#Environment%20configuration)
- [Wdrożenie aplikacji](#Wdrożenie%20aplikacji)
- [Pricing](#Pricing)
- [See also](#See%20also)

# Komponenty i koncepcje

[źródło](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.html)

## Platform

**Platforma** to połączenie:

- systemu operacyjnego,
- środowiska uruchomieniowego języka programowania,
- serwera WWW,
- serwera aplikacji,
- i komponentów Elastic Beanstalk.

### Obsługiwane platformy

[źródło](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.platforms.html)

Projektujesz i kierujesz swoją aplikację internetową na platformę. Elastic Beanstalk udostępnia różne platformy, na których można budować aplikacje:

- Go,
- Java,
- .NET,
- Node.js,
- PHP,
- Python,
- Ruby,

Odługuje również kontenery Docker:

- pojedynczy kontener Docker,
- klaster kontenerów Docker,
- docker prekonfigurowany.

## Application

**Aplikacja** Elastic Beanstalk to logiczny zbiór komponentów Elastic Beanstalk, w tym:

- [Environment](#Environment)s,
- [Application Version](#Application%20Version)s,
- [Environment configuration](#Environment%20configuration)s.

W Elastic Beanstalk **aplikacja** jest koncepcyjnie podobna do folderu.

## Application Version

W Elastic Beanstalk, **wersja aplikacji** odnosi się do konkretnej, oznaczonej iteracji kodu możliwego do wdrożenia dla aplikacji internetowej. **Wersja aplikacji** wskazuje na obiekt [S3](S3.md), który zawiera kod możliwy do wdrożenia (np. plik Java WAR).

**Wersja aplikacji** jest częścią [Application](#Application). [Application](#Application) może mieć wiele **wersji**, a każda **wersja** jest unikalna.

> W działającym środowisku możesz wdrożyć dowolną wersję aplikacji, którą już przesłałeś do aplikacji, lub możesz przesłać i natychmiast wdrożyć nową wersję aplikacji. Możesz załadować wiele wersji aplikacji, aby przetestować różnice pomiędzy jedną wersją aplikacji internetowej a drugą.

## Environment

**Środowisko** to zbiór zasobów AWS, na których działa [Application Version](#Application%20Version). Tworząc środowisko, Elastic Beanstalk dostarcza zasoby potrzebne do uruchomienia określonej przez Ciebie [Application Version](#Application%20Version).

Każde **środowisko** uruchamia tylko jedną [Application Version](#Application%20Version) w danym momencie, jednak można uruchomić tę samą lub różne [Application Version](#Application%20Version)s na wielu środowiskach jednocześnie.

## Environment Tier

Aby uruchamić [Environment](#Environment), trzeba najpierw wybrać **warstwę środowiska**. **Environment Tier** określa typ [Application](#Application) uruchamianej w [Environment](#Environment) i decyduje o zasobach, które Elastic Beanstalk dostarcza do jej obsługi.

Dostępne są 2 **warstwy środowiska**:

- [Web server environment](#Web%20server%20environment) - dla aplikacji obsługujących żądania HTTP,
- [Worker environment](#Worker%20environment) -  dla aplikacji backendowych, które pobiera zadania z kolejki Amazon [SQS](SQS.md).

### Web server environment

[źródło](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts-webserver.html)

Zasoby AWS tworzone dla warstwy **środowiska web server** obejmują:

- jeden [ELB](Elastic%20Load%20Balancing.md),
- jedną [Auto Scaling Group](EC2%20Auto%20Scaling.md#Auto%20Scaling%20Group),
- jedną lub więcej [EC2](EC2.md).

![Beanstalk web server.png](Beanstalk%20web%20server.png.md)

Każde **środowisko** posiada rekord [CNAME](../network/DNS.md#CNAME) (adres URL), który wskazuje na [load balanser](Elastic%20Load%20Balancing.md). Ten adres URL jest aliasowany w [Route 53](Route%2053.md) do adresu URL [Elastic Load Balancing](Elastic%20Load%20Balancing.md) za pomocą [CNAME](../network/DNS.md#CNAME).

### Worker environment

[źródło](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts-worker.html)

Zasoby AWS utworzone dla warstwy **Worker environment** obejmują:

- jedną [Auto Scaling Group](EC2%20Auto%20Scaling.md#Auto%20Scaling%20Group),
- kolejkę Amazon [SQS](SQS.md),
- jedną lub więcej [EC2](EC2.md) z doinstalowanym daemonem [SQS](SQS.md),
- [IAM Role](IAM.md#IAM%20Role).

![Beanstalk worker.png](Beanstalk%20worker.png.md)

#### Daemon SQS

Po uruchomieniu **środowiska robotniczego**, Elastic Beanstalk instaluje niezbędne pliki pomocnicze dla wybranego języka programowania oraz **daemon** na każdej instancji [EC2](EC2.md) w  [Auto Scaling Group](EC2%20Auto%20Scaling.md#Auto%20Scaling%20Group). Każdy z deamonów na wielu [EC2](EC2.md) czyta z tej samej kolejki [SQS](SQS.md).

**Daemon** odczytuje wiadomości z kolejki [SQS](SQS.md), a następnie wysyła dane w celu przetworzenia do [Application](#Application) uruchomionej w **środowisku robotniczym**.

## Environment configuration

**Konfiguracja środowiska** identyfikuje zbiór parametrów i ustawień, które definiują zachowanie [Environment](#Environment) i powiązanych z nim zasobów. Kiedy aktualizujesz ustawienia **konfiguracji środowiska**, Elastic Beanstalk automatycznie stosuje zmiany do istniejących zasobów lub usuwa i wdraża nowe zasoby (w zależności od rodzaju zmiany).

# Wdrożenie aplikacji

Aby wdrożyć aplikację na **Elastic Beanstalk**, należy przesłać [Application Version](#Application%20Version) w postaci pakietu źródłowego (na przykład plik Java .war), a następnie podać pewne informacje o niej.

Elastic Beanstalk automatycznie uruchamia [Environment](#Environment) oraz tworzy i konfiguruje zasoby AWS potrzebne do uruchomienia kodu. Po uruchomieniu [Environment](#Environment), można nim zarządzać i wdrażać nowe [Application Version](#Application%20Version).

![Beanstalk flow.png](Beanstalk%20flow.png.md)

# Pricing

Sam **Beanstalk** jest darmowy, lecz płąci się za zarządzane przez niego zasoby.

---

# See also

- [EC2](EC2.md)
- [Elastic Block Store](Elastic%20Block%20Store.md)
- [Security group](Security%20group.md)
- [Elastic Load Balancing](Elastic%20Load%20Balancing.md)
- [Auto Scaling](Auto%20Scaling.md)
