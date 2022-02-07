#AWS #compute

[źródło](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html)

**Elastic Beanstalk** umożliwia szybkie wdrażanie aplikacje w chmurze AWS i zarządzanie nimi bez konieczności poznawania infrastruktury, na której działają te aplikacje. Wystarczy załadować aplikację, a Elastic Beanstalk automatycznie zajmie się szczegółami dostarczania, m.in:

- pojemności ([[EC2]]),
- środowiska uruchomieniowego (np. JRE, Python 3 RE)
- pamięci masowej ([[Elastic Block Store]]),
- firewalla ([[Security group]]),
- równoważenia obciążenia ([[Elastic Load Balancing]]),
- skalowania ([[Auto Scaling]]),
- monitorowania stanu aplikacji.

Nadal jednaj pozostaje możliwość konfiguracji [[#Environment|środowiska]] Beanstalk.

##### Spis treści

- [[#Komponenty i koncepcje]]
  - [[#Platform]]
    - [[#Obsługiwane platformy]]
  - [[#Application]]
  - [[#Application Version]]
  - [[#Environment]]
  - [[#Environment Tier]]
    - [[#Web server environment]]
    - [[#Worker environment]]
      - [[#Daemon SQS]]
  - [[#Environment configuration]]
- [[#Wdrożenie aplikacji]]
- [[#Pricing]]
- [[#See also]]

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

- [[#Environment]]s,
- [[#Application Version]]s,
- [[#Environment configuration]]s.

W Elastic Beanstalk **aplikacja** jest koncepcyjnie podobna do folderu.

## Application Version

W Elastic Beanstalk, **wersja aplikacji** odnosi się do konkretnej, oznaczonej iteracji kodu możliwego do wdrożenia dla aplikacji internetowej. **Wersja aplikacji** wskazuje na obiekt [[S3]], który zawiera kod możliwy do wdrożenia (np. plik Java WAR).

**Wersja aplikacji** jest częścią [[#Application]]. [[#Application]] może mieć wiele **wersji**, a każda **wersja** jest unikalna.

> W działającym środowisku możesz wdrożyć dowolną wersję aplikacji, którą już przesłałeś do aplikacji, lub możesz przesłać i natychmiast wdrożyć nową wersję aplikacji. Możesz załadować wiele wersji aplikacji, aby przetestować różnice pomiędzy jedną wersją aplikacji internetowej a drugą.

## Environment

**Środowisko** to zbiór zasobów AWS, na których działa [[#Application Version]]. Tworząc środowisko, Elastic Beanstalk dostarcza zasoby potrzebne do uruchomienia określonej przez Ciebie [[#Application Version]].

Każde **środowisko** uruchamia tylko jedną [[#Application Version]] w danym momencie, jednak można uruchomić tę samą lub różne [[#Application Version]]s na wielu środowiskach jednocześnie.

## Environment Tier

Aby uruchamić [[#Environment]], trzeba najpierw wybrać **warstwę środowiska**. **Environment Tier** określa typ [[#Application]] uruchamianej w [[#Environment]] i decyduje o zasobach, które Elastic Beanstalk dostarcza do jej obsługi.

Dostępne są 2 **warstwy środowiska**:

- [[#Web server environment]] - dla aplikacji obsługujących żądania HTTP,
- [[#Worker environment]] -  dla aplikacji backendowych, które pobiera zadania z kolejki Amazon [[SQS]].

### Web server environment

[źródło](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts-webserver.html)

Zasoby AWS tworzone dla warstwy **środowiska web server** obejmują:

- jeden [[Elastic Load Balancing|ELB]],
- jedną [[EC2 Auto Scaling#Auto Scaling Group|Auto Scaling Group]],
- jedną lub więcej [[EC2]].

![[Beanstalk web server.png]]

Każde **środowisko** posiada rekord [[DNS#CNAME|CNAME]] (adres URL), który wskazuje na [[Elastic Load Balancing|load balanser]]. Ten adres URL jest aliasowany w [[Route 53]] do adresu URL [[Elastic Load Balancing]] za pomocą [[DNS#CNAME|CNAME]].

### Worker environment

[źródło](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts-worker.html)

Zasoby AWS utworzone dla warstwy **Worker environment** obejmują:

- jedną [[EC2 Auto Scaling#Auto Scaling Group|Auto Scaling Group]],
- kolejkę Amazon [[SQS]],
- jedną lub więcej [[EC2]] z doinstalowanym daemonem [[SQS]],
- [[IAM#IAM Role|IAM Role]].

![[Beanstalk worker.png]]

#### Daemon SQS

Po uruchomieniu **środowiska robotniczego**, Elastic Beanstalk instaluje niezbędne pliki pomocnicze dla wybranego języka programowania oraz **daemon** na każdej instancji [[EC2]] w  [[EC2 Auto Scaling#Auto Scaling Group|Auto Scaling Group]]. Każdy z deamonów na wielu [[EC2]] czyta z tej samej kolejki [[SQS]].

**Daemon** odczytuje wiadomości z kolejki [[SQS]], a następnie wysyła dane w celu przetworzenia do [[#Application]] uruchomionej w **środowisku robotniczym**.

## Environment configuration

**Konfiguracja środowiska** identyfikuje zbiór parametrów i ustawień, które definiują zachowanie [[#Environment]] i powiązanych z nim zasobów. Kiedy aktualizujesz ustawienia **konfiguracji środowiska**, Elastic Beanstalk automatycznie stosuje zmiany do istniejących zasobów lub usuwa i wdraża nowe zasoby (w zależności od rodzaju zmiany).

# Wdrożenie aplikacji

Aby wdrożyć aplikację na **Elastic Beanstalk**, należy przesłać [[#Application Version]] w postaci pakietu źródłowego (na przykład plik Java .war), a następnie podać pewne informacje o niej.

Elastic Beanstalk automatycznie uruchamia [[#Environment]] oraz tworzy i konfiguruje zasoby AWS potrzebne do uruchomienia kodu. Po uruchomieniu [[#Environment]], można nim zarządzać i wdrażać nowe [[#Application Version]].

![[Beanstalk flow.png]]

# Pricing

Sam **Beanstalk** jest darmowy, lecz płąci się za zarządzane przez niego zasoby.

---

# See also

- [[EC2]]
- [[Elastic Block Store]]
- [[Security group]]
- [[Elastic Load Balancing]]
- [[Auto Scaling]]
