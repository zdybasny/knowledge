#AWS #network

Jednym z aspektów skalowalności (elastycznoći) jest sposób, w jaki kierujemy użytkowników do odpowiedniej infrastruktury, za co odpowiedzialny jest Elastic Load Balancing. ELB umożliwia dystrybucję ruchu do wielu miejsc docelowych, np. oparciu o obciążenie każdego z dostępnych serwerów. Obsługuje jedną lub więcej [[AWS locations#Availability zone|Availaility zone]]s w ramach [[AWS locations#Region|regionu]].

Domyślnie integruje się z [[EC2]], [[Elastic Container Services]].

> Load balancer działa jako pojedynczy punkt kontaktu dla całego ruchu przychodzącego z sieci do [[EC2#Auto Scaling group|Auto Scaling group]]. Chociaż Elastic Load Balancing i [[EC2#Amazon EC2 Auto Scaling|Amazon EC2 Auto Scaling]] są oddzielnymi usługami, współpracują one ze sobą, aby zapewnić wysoką wydajność i dostępność aplikacji działających w Amazon EC2.

##### Spis treści

- [[#Typy Load Balancerów]]
  - [[#Porónanie typów]]
  - [[#Application Load Balancer]]
    - [[#Algorytm ALB]]
    - [[#Komponenty i właściwośći ALB]]
    - [[#Podsieci i strefy dla ALB]]
      - [[#Availability Zone]]
      - [[#Local Zone]]
  - [[#Network Load Balancer]]
    - [[#Algorytm NLB]]
    - [[#Komponenty i właściwośći NLB]]
  - [[#Gateway Load Balancer]]
    - [[#Komponenty i właściwośći GWLB]]
  - [[#Classic Load Balancer]]
    - [[#Komponenty i właściwośći CLB]]
    - [[#EC2-Classic]]
- [[#Komponenty Load Balancerów]]
  - [[#Security group]]
  - [[#Listener]]
    - [[#Rule]]
  - [[#Certyfikaty SSL dla ELB]]
    - [[#Tworzenie certyfikatu]]
  - [[#Target group]]
    - [[#Typy target group]]
      - [[#Intanceses vs IP addresses]]
    - [[#Auto Scaling Group]]
  - [[#Cross-zone load balancing]]
  - [[#Zonal isolation]]
  - [[#Health Check]]
  - [[#Slow start]]
  - [[#Flow Stickiness]]
  - [[#Sticky session]]
    - [[#Sesja bazująca na cookies]]
      - [[#Uwagi]]
      - [[#Duration-based cookies]]
      - [[#Application-based cookies]]
    - [[#Sesja NLB]]
    - [[#Zalety i wady sticky session]]
  - [[#Deregistration Delay]]
  - [[#Connection Draining]]
- [[#See also]]


# Typy Load Balancerów

Istnieją 4 rodzaje load balancerów:

- [[#Application Load Balancer]] (ALB) - do trasowania ruchu HTTP/HTTPS (lub [[Model OSI#Layer 7|warstwy 7]])

- [[#Network Load Balancer]] (ELB) - do trasowania ruchu TCP lub UDP (lub [[Model OSI#Layer 4|warstwy 4]])

- [[#Gateway Load Balancer]] (GWLB) - do trasowania ruchu TCP

- [[#Classic Load Balancer]] (CLB)  `deprecated` - podstawowe LB pomiędzy [[EC2]]

## Porónanie typów

[źródło](https://aws.amazon.com/elasticloadbalancing/features/?nc=sn&loc=2)

|                                                       | Application Load Balancer |            Network Load Balancer            |                         Gateway Load Balancer                        |             Classic Load Balancer             |
| :---------------------------------------------------- | :-----------------------: | :-----------------------------------------: | :------------------------------------------------------------------: | :-------------------------------------------: |
| **[[Model OSI]] layers**                              |   [[Model OSI#Layer 7]]   |            [[Model OSI#Layer 4]]            | [[Model OSI#Layer 3]] Gateway + [[Model OSI#Layer 4]] Load Balancing | [[Model OSI#Layer 4]] / [[Model OSI#Layer 7]] |
| **[[#Listener]]'s rule protocols**                    |     HTTP, HTTPS, gRPC     |                TCP, UDP, TLS                |                                  IP                                  |           TCP, SSL/TLS, HTTP, HTTPS           |
| **[[#Target group]] type**                            |  IP, [[EC2]], [[Lambda]]  | IP, [[EC2]], [[#Application Load Balancer]] |                              IP, [[EC2]]                             |       [[EC2]] (**no TG, directly only**)      |
|                                                       |                           |                                             |                                                                      |                                               |
| **ECS hostowane na EC2**                              |             V             |                      V                      |                                                                      |                       V                       |
| **ECS hostowane na Fargate**                          |             V             |                      V                      |                                                                      |                                               |
|                                                       |                           |                                             |                                                                      |                                               |
| [[#Health Check]]                                     |             V             |                      V                      |                                   V                                  |                       V                       |
| [[#Security group]]                                   |             v             |                                             |                                                                      |                       v                       |
| Outpost support                                       |             V             |                                             |                                                                      |                                               |
| [[#Local Zone]]                                       |             V             |                                             |                                                                      |                                               |
| IP address - Static, Elastic                          |                           |                      V                      |                                                                      |                                               |
| Slow start                                            |             V             |                                             |                                                                      |                                               |
| Connection draining ([[#Deregistration Delay]])       |             V             |                      V                      |                                   V                                  |                       V                       |
| Configurable idle connection timeout                  |             V             |                                             |                                                                      |                                               |
| PrivateLink Support                                   |                           |                   TCP, TLS                  |                                 GWLBE                                |                                               |
| [[#Zonal isolation]]                                  |                           |                      V                      |                                   V                                  |                                               |
| Session resumption                                    |             V             |                      V                      |                                                                      |                                               |
| [[#Flow Stickiness]]                                  |         Symmetric         |                  Symmetric                  |                               Symmetric                              |                   Symmetric                   |
| [[#Sticky session]]                                   |             V             |                      V                      |                                                                      |                       V                       |
| Long-lived TCP connection                             |                           |                      V                      |                                   V                                  |                                               |
| Load Balancing to multiple ports on the same instance |             V             |                      V                      |                                   V                                  |                                               |
| Load Balancer deletion protection                     |             V             |                      V                      |                                   V                                  |                                               |
| Preserve Source IP address                            |             V             |                      V                      |                                   V                                  |                                               |
| WebSockets                                            |             V             |                      V                      |                                   V                                  |                                               |
| Supported network/Platforms                           |          [[VPC]]          |                   [[VPC]]                   |                                [[VPC]]                               |           [[#EC2-Classic]], [[VPC]]           |
| [[#Cross-zone load balancing]]                        |             V             |                      V                      |                                   V                                  |                       V                       |
| IAM Permissions (Resource, Tag based)                 |             V             |                      V                      |                                   V                                  |              Only resource based              |

## Application Load Balancer

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)

**Application Load Balancer** podejmuje decyzje dotyczące routingu w [[Model OSI#Layer 7|warstwie aplikacji]] (HTTP/HTTPS). Obsługuje routing oparty na ścieżkach i zawartości zapytań. Routing jest wykonywany niezależnie dla każdej [[#Target group]], nawet jeśli cel jest zarejestrowany w wielu grupach docelowych.

Serwery aplikacji nie widzą IP clienta bezpośrednio, tylko adres ALB. Prawdziwy adres klienta dostępny przesyłany jest w nagłówkach zapytań **X-Forwarded-For**, **X-Forwarded-Port**, **X-Forwarded-Proto**,

Nazwa hosta dla ALB jest stała: **XXX.region.elb.amazonaws.com**.

![[ALB_NLB.png]]

### Algorytm ALB

Można skonfigurować algorytm routingu używany na poziomie [[#Target group]]. Domyślnym algorytmem routingu jest **round robin**; alternatywnie można określić algorytm routingu **najmniej zaległych żądań**.

### Komponenty i właściwośći ALB

- [[#Security group]]
- [[#Listener]]
- [[#Certyfikaty SSL dla ELB]]
- [[#Target group]]
- [[#Health Check]]
- [[#Slow start]]
- [[#Flow Stickiness]]
- [[#Sticky session]]
- [[#Cross-zone load balancing]]
- [[#Deregistration Delay]]

### Podsieci i strefy dla ALB

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/application-load-balancers.html#subnets-load-balancer)

Kiedy tworzysz Application Load Balancer, musisz określić jeden z następujących typów podsieci:

- [[#Availability Zone]],
- [[#Local Zone]],
- Outpost.

#### Availability Zone

Należy wybrać co najmniej dwie [[VPC Subnet]]. Obowiązują następujące ograniczenia:

- Każda [[VPC Subnet]] musi pochodzić z innej strefy [[AWS locations#Availability zone|AZ]].

- Aby upewnić się, że Load Balancer może się prawidłowo skalować, sprawdź, czy każda [[VPC Subnet]] dla Load Balancera ma blok [[CIDR]] z co najmniej **/27** bitmaską (na przykład 10.0.0.0/27) i co najmniej 8 wolnych adresów IP na [[VPC Subnet]]. Twój load balancer używa tych adresów IP do nawiązywania połączeń z celami. W zależności od profilu ruchu, Load Balancer może skalować się wyżej i zużywać maksymalnie do 100 adresów IP rozłożonych na wszystkie włączone [[VPC Subnet]]

#### Local Zone

Można określić jedną lub więcej [[VPC Subnet]] strefy [[AWS locations#Local zone|lokalnej]]. Obowiązują następujące ograniczenia:

- Nie możesz używać [[AWS WAF]] z load balancerem.

- Nie można używać funkcji [[Lambda]] jako celu.

## Network Load Balancer

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html)

Network Load Balancer funkcjonuje w [[Model OSI#Layer 4|warstwie transportu]] OSI. Może obsłużyć miliony żądań na sekundę. Po otrzymaniu przez Load Balancer żądania połączenia, wybiera on cel z [[#Target group]] dla domyślnej [[#Rule]]. Próbuje otworzyć połączenie TCP do wybranego celu na porcie określonym w konfiguracji [[#Listener]]a.

NLB tworzy interfejs sieciowy dla każdej [[AWS locations#Availability zone|AZ]], którą włączysz. Każdy węzeł równoważenia obciążenia w strefie dostępności używa tego interfejsu sieciowego, aby uzyskać **statyczny adres IP**. Kiedy tworzysz load balancer wychodzący do Internetu, możesz opcjonalnie przypisać jeden [[Elastic IP]] na [[VPC Subnet]].

![[ALB_NLB.png]]

### Algorytm NLB

Dla ruchu TCP, NLB wybiera cel używając algorytmu **flow hash** opartego na:

- protokole,
- źródłowym adresie IP,
- źródłowym porcie,
- docelowym adresie IP,
- docelowym porcie i
- numerze sekwencji TCP.

Połączenia TCP od klienta mają różne porty źródłowe i numery sekwencji, i mogą być kierowane do różnych celów. Każde pojedyncze połączenie TCP jest kierowane do jednego celu przez cały czas trwania połączenia (**long live connect**).

### Komponenty i właściwośći NLB

- [[#Listener]]
- [[#Certyfikaty SSL dla ELB]]
- [[#Target group]]
- [[#Health Check]]
- [[#Flow Stickiness]]
- [[#Sticky session]]
- [[#Cross-zone load balancing]]
- [[#Zonal isolation]]
- [[#Deregistration Delay]]

## Gateway Load Balancer

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html)

**GWLB** umożliwia wdrażanie, skalowanie i zarządzanie wirtualnymi urządzeniami, takimi jak zapory sieciowe, systemy wykrywania i zapobiegania włamaniom oraz systemy głębokiej inspekcji pakietów. Łączy w sobie przejrzystą [[Internet Gateway]] (czyli pojedynczy punkt wejścia i wyjścia dla całego ruchu) i dystrybuuje ruch, jednocześnie skalując urządzenia wirtualne wraz z zapotrzebowaniem.

Gateway Load Balancer działa na [[Model OSI#Layer 3|trzeciej warstwie]] (warstwie sieciowej) [[Model OSI|modelu OSI]]. Nasłuchuje wszystkich pakietów IP na wszystkich portach i przekazuje ruch do [[#Target group]], która jest określona w [[#Rule|regule nasłuchu]]. Utrzymuje [[#Flow Stickiness]] do konkretnego urządzenia docelowego używając 5-tuple (dla przepływów TCP/UDP) lub 3-tuple (dla przepływów innych niż TCP/UDP).

Gateway Load Balancer i jego zarejestrowane instancje wirtualnych urządzeń wymieniają ruch aplikacyjny za pomocą protokołu GENEVE na porcie 6081. Obsługuje on maksymalny rozmiar jednostki transmisji (MTU) 8500 bajtów.

![[GWLB.png]]

Gateway Load Balancer używa punktów końcowych (**endpoint**) Gateway Load Balancer do bezpiecznej wymiany ruchu pomiędzy granicami [[VPC]]. Endpoint GWLB jest punktem końcowym [[VPC]], który zapewnia prywatną łączność między urządzeniami wirtualnymi w [[VPC]] dostawcy usług a serwerami aplikacji w [[VPC]] odbiorcy usług. Wdrażasz Gateway Load Balancer w tym samym [[VPC]] co urządzenia wirtualne. Urządzenia wirtualne są rejestrowane w [[#Target group]] dla urządzenia Gateway Load Balancer.

Ruch do i z punktu końcowego Gateway Load Balancer jest konfigurowany za pomocą [[VPC Route table]]. Ruch przepływa z [[VPC]] odbiorcy usług przez punkt końcowy Gateway Load Balancer do Gateway Load Balancer w [[VPC]] dostawcy usług, a następnie powraca do VPC odbiorcy usług. Należy utworzyć punkt końcowy Gateway Load Balancer i serwery aplikacji w różnych [[VPC Subnet]]. Dzięki temu można skonfigurować punkt końcowy Gateway Load Balancer jako następny skok w [[VPC Route table]] dla [[VPC Subnet]] aplikacji.

### Komponenty i właściwośći GWLB

- [[#Listener]]
- [[#Target group]]
- [[#Health Check]]
- [[#Flow Stickiness]]
- [[#Cross-zone load balancing]]
- [[#Zonal isolation]]
- [[#Deregistration Delay]]

## Classic Load Balancer

`deprecated`

[źródło]([What is a Classic Load Balancer? - Elastic Load Balancing (amazon.com)](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/introduction.html))

CLB zapewnia podstawowe równoważenie obciążenia pomiędzy wieloma [[EC2]] i działa zarówno na poziomie żądania, jak i połączenia. CLB jest przeznaczony dla aplikacji, które są zbudowane w sieci [[#EC2-Classic]].

![[CLB.png]]

Zalecany jest:

- [[#Application Load Balancer]] dla ruchu w [[Model OSI#Layer 7|warstwie 7]] i
- [[#Network Load Balancer]] dla ruchu w [[Model OSI#Layer 4|warstwie 4]]

podczas korzystania z [[VPC]].

Mimo wyraźnego zalecenia używania ALB, CLB może dostarczać kożyśći:

- wspracie dla [[#EC2-Classic]],
- wsparcie dla listenerów TPC i SSL,
- wsparcie dla Sticky Sesstion używających coockies generowanych przez aplikacje.

### Komponenty i właściwośći CLB

- [[#Security group]]
- [[#Listener]]
- [[#Certyfikaty SSL dla ELB]]
- [[#Health Check]]
- [[#Flow Stickiness]]
- [[#Sticky session]]
- [[#Cross-zone load balancing]]
- [[#Connection Draining]]

### EC2-Classic

`deprecated`

Z EC2-Classic, twoje instancje działają w jednej, płaskiej sieci, którą dzielisz z innymi klientami.
Platforma EC2-Classic została wprowadzona w oryginalnym wydaniu Amazon EC2. Jeśli utworzyłeś swoje konto AWS po 2013-12-04, nie obsługuje EC2-Classic, więc musisz uruchomić swoje instancje Amazon EC2 w VPC.

# Komponenty Load Balancerów

## Security group

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-update-security-groups.html)

Dostępne dla: [[#Application Load Balancer|ALB]], [[#Classic Load Balancer|CLB]].

Musisz upewnić się, że LB może komunikować się z zarejestrowanymi celami zarówno na porcie [[#Listener]]a jak i porcie [[#Health Check]]. Za każdym razem, gdy dodajesz [[#Listener]] do LB lub aktualizujesz port [[#Health Check]] dla [[#Target group]] używanej przez LB do [[NAT|trasowania]] żądań, musisz sprawdzić, czy [[Security group]] skojarzone z LB zezwalają na ruch na nowym porcie w obu kierunkach. W [[VPC]] podajesz [[Security group]] dla load balancera, co pozwala Ci wybrać porty i protokoły, które mają być dozwolone.

## Listener

Dostępne dla: [[#Application Load Balancer|ALB]], [[#Network Load Balancer|NLB]], [[#Gateway Load Balancer|GWLB]], [[#Classic Load Balancer|CLB]].

**Listener** sprawdza żądania połączeń od klientów, używając:

- protokołu i
- portu,

które konfigurujesz.

### Rule

**Reguły**, które definiujesz dla listenera określają, w jaki sposób load balancer kieruje żądania do zarejestrowanych celów. Każda reguła składa się z:

- **priorytetu**,
- **akcji** (1+) i
- **warónków** (1+).

Gdy warunki dla danej reguły są spełnione, wówczas wykonywane są jej akcje. Musisz zdefiniować domyślną regułę dla każdego listenera i możesz opcjonalnie zdefiniować dodatkowe reguły.

|               | Application Load Balancer | Network Load Balancer | Gateway Load Balancer |   Classic Load Balancer   |
| :------------ | :-----------------------: | :-------------------: | :-------------------: | :-----------------------: |
| **Protocols** |     HTTP, HTTPS, gRPC     |     TCP, UDP, TLS     |           IP          | TCP, SSL/TLS, HTTP, HTTPS |

## Certyfikaty SSL dla ELB

#secutiry

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-https-listener.html)

Dostępne dla: [[#Application Load Balancer|ALB]], [[#Network Load Balancer|NLB]],  [[#Classic Load Balancer|CLB]].

Aby użyć [[#Listener]] HTTPS, musisz wdrożyć co najmniej jeden certyfikat X.509 (sertyfikat serwera [[SSL-TLS]]) na swoim load balancerze. Load balancer używa certyfikatu do zakończenia połączenia front-end, a następnie odszyfrowuje żądania od klientów przed wysłaniem ich do celów.

![[ELB SSL.png]]

### Tworzenie certyfikatu

- Podczas tworzenia listenera HTTPS należy podać dokładnie jeden certyfikat. Certyfikat ten nazywany jest **certyfikatem domyślnym**. Po utworzeniu listenera HTTPS można zastąpić domyślny certyfikat.

- Podczas tworzenia certyfikatu do użycia z load balancerem należy określić [[DNS|nazwę domeny]].

- Klient może użyć Server Name Indication ([[SSL-TLS#Server Name Indication|SNI]]), aby nazwę hosta. Dostępne dla: [[#Application Load Balancer|ALB]], [[#Network Load Balancer|NLB]].

- Można określić dodatkowe certyfikaty na **liście certyfikatów** do obsługi wielu domen. W takim przypadku domyślny certyfikat jest używany tylko wtedy, gdy klient łączy się bez użycia protokołu [[SSL-TLS#Server Name Indication|SNI]] do określenia nazwy hosta lub gdy na liście certyfikatów nie ma pasujących certyfikatów.

> Zaleca się tworzenie certyfikatów dla load balancera za pomocą [[AWS Certificate Manager]] (ACM). ACM integruje się z Elastic Load Balancing, dzięki czemu można wdrożyć certyfikat na load balancerze.

> Alternatywnie można użyć narzędzi SSL/TLS do utworzenia żądania podpisania certyfikatu ([[SSL-TLS#CSR|CSR]]), następnie uzyskać podpisanie [[SSL-TLS#CSR|CSR]] przez [[SSL-TLS#CA|CA]] w celu utworzenia certyfikatu, a następnie zaimportować certyfikat do ACM lub przesłać certyfikat do AWS Identity and Access Management ([[IAM]]).

Jeśli nie określisz dodatkowych certyfikatów, ale musisz hostować wiele bezpiecznych aplikacji za pośrednictwem jednego load balancera, możesz użyć certyfikatu wieloznacznego lub dodać do certyfikatu Subject Alternative Name (SAN) dla każdej dodatkowej domeny.

## Target group

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)

Dostępne dla: [[#Application Load Balancer|ALB]], [[#Network Load Balancer|NLB]], [[#Gateway Load Balancer|GWLB]].

**Target group** kieruje żądania do jednego lub więcej zarejestrowanych celów (np. [[EC2]]), używając protokołu i portu, który określisz. Możesz zarejestrować jeden cel z wieloma grupami docelowymi.

|                       | Application Load Balancer |            Network Load Balancer            | Gateway Load Balancer | Classic Load Balancer |
| :-------------------- | :-----------------------: | :-----------------------------------------: | :-------------------: | :-------------------: |
| **Target group type** |  IP, [[EC2]], [[Lambda]]  | IP, [[EC2]], [[#Application Load Balancer]] |      IP, [[EC2]]      |                       |

### Typy target group

**Instances**

- Obsługuje load balancing do [[EC2]] w ramach określonego [[VPC]].

**IP addresses**

- Wspiera load balancing do [[VPC]] i zasobów lokalnych, IP musi być prywatny.
- Ułatwia routing do wielu **adresów IP** i interfejsów sieciowych na tej samej instancji.
- Oferuje elastyczność w architekturach opartych na mikroserwisach, upraszczając komunikację między aplikacjami.

**Lambda function**

- Ułatwia routing do pojedynczej funkcji [[Lambda]].

**Application Load Balancer**

- Oferuje elastyczność [[#Network Load Balancer|NLB]] w akceptowaniu i trasowaniu zapytań TCP w ramach danego [[VPC]].
- Ułatwia używanie statycznych IP oraz PrivateLink razem z [[#Application Load Balancer|ALB]].

#### Intanceses vs IP addresses

Instances:

- Typ docelowy [[EC2]] jest ograniczony tylko do niektórych instancji. Powinien to być domyślny wybór podczas load balancingu instancji.
- Na przykład, jeśli masz instancje w [[AWS Auto Scaling#Auto Scaling group|Auto Scaling group]] (ASG), ASG może automatycznie zarejestrować twoje instancje z twoim load balancerem. Nie możesz tego zrobić dla typów docelowych IP.

IP adresses:

- Cel nie musi być instancją - wszystko z prywatnym adresem IP będzie działać, w tym wewnętrzny load balance, prywatna usługa VPC, kontenery Fargate, bazy danych, serwery on-premise przez VPN.
- Cel może być w innym regionie, tak długo, jak masz cross-region peering między swoimi VPC masz wiele interfejsów sieciowych na swojej instancji, więc możesz rozdzielać ruch pomiędzy nimi, np. różne aplikacje na jednej instancji są przypisane do różnych interfejsów.
- Każdy interfejs może być powiązany z inną grupą docelową.

### Auto Scaling Group

Po dołączeniu [[#Target group|target grupy]] do [[EC2 Auto Scaling#Auto Scaling Group|Auto Scaling Group]], [[EC2 Auto Scaling]] rejestruje swoje cele z [[Elastic Load Balancing#Target group|Target group]] dla Ciebie, gdy je uruchamia, a [[EC2]], które są kończone przez Auto Scaling są automatycznie wyrejestrowywane z load balancera.

**Więcej: [[EC2 Auto Scaling]].**

## Cross-zone load balancing

[źródło]()

Dostępne dla: [[#Application Load Balancer|ALB]], [[#Network Load Balancer|NLB]], [[#Gateway Load Balancer|GWLB]], [[#Classic Load Balancer|CLB]].

Po włączeniu [[AWS locations#Availability zone|Availability zone]] dla load balancera, Elastic Load Balancing tworzy węzeł load balancera w [[AWS locations#Availability zone|AZ]]. Domyślnie każdy węzeł balansera obciążenia dystrybuuje ruch pomiędzy zarejestrowanymi celami tylko w swojej  [[AWS locations#Availability zone|AZ]].

Jeśli włączysz **międzystrefowe równoważenie obciążenia**, każdy węzeł równoważenia obciążenia dystrybuuje ruch między zarejestrowanymi celami we wszystkich włączonych [[AWS locations#Availability zone|AZ]].

![[ELB Cross-Zone LB.png]]

## Zonal isolation

[źródło](https://aws.amazon.com/elasticloadbalancing/network-load-balancer/)

Dostępne dla: [[#Network Load Balancer|NLB]], [[#Gateway Load Balancer|GWLB]].

Chociaż zaleca się skonfigurowanie load balancerów i celów w wielu [[AWS locations#Availability zone|AZ]] w celu osiągnięcia wysokiej dostępności, Load Balancer może być włączony w pojedynczej [[AWS locations#Availability zone|AZ]], aby wspierać architektury, które wymagają izolacji strefowej.

## Health Check

Dostępne dla: [[#Application Load Balancer|ALB]], [[#Network Load Balancer|NLB]], [[#Gateway Load Balancer|GWLB]], [[#Classic Load Balancer|CLB]].

Elastic Load Balancer kieruje ruch tylko do zdrowych celów, takich jak instancje [[EC2]], kontenery, adresy IP, mikroserwisy, funkcje [[Lambda]] i urządzenia. Health Checki są kluczowe, gdyż informują LB, czy [[#Target group]]s (lub [[EC2]] dla [[#Classic Load Balancer|CLB]]) do nich podpięte są dostępne.

FSprawdzenie zdrowia jest wykonywane na:

- porcie (**port**) i
- trasie (**route**).

Jeśli odpowiedzią nie jest 200 (OK), to instancja jest niezdrowa (**unhealthy**).

## Slow start

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#slow-start-mode)

Dostępne dla: [[#Application Load Balancer|ALB]].

Domyślnie, cel zaczyna otrzymywać swój pełny udział w żądaniach tak szybko, jak tylko zostanie zarejestrowany w [[#Target group]] i przejdzie [[#Health Check]]. Użycie trybu powolnego startu daje celom czas na rozgrzanie się zanim load balancer wyśle do nich pełną część żądań.

Po włączeniu powolnego startu dla [[#Target group]], jej cele wchodzą w tryb powolnego startu, gdy zostaną uznane za zdrowe przez [[#Target group]]. Cel w trybie powolnego startu opuszcza tryb powolnego startu, gdy upłynie skonfigurowany czas trwania powolnego startu lub cel staje się niezdrowy. Load balancer liniowo zwiększa liczbę żądań, które może wysłać do celu w trybie powolnego startu. Po tym jak zdrowy cel opuści tryb powolnego startu, load balancer może wysyłać do niego pełny udział żądań.

## Flow Stickiness

[źródło](https://aws.amazon.com/elasticloadbalancing/features/?nc=sn&loc=2)

Dostępne dla: [[#Application Load Balancer|ALB]], [[#Network Load Balancer|NLB]], [[#Gateway Load Balancer|GWLB]], [[#Classic Load Balancer|CLB]].

Wszystkie pakiety przepływu są wysyłane do jednego celu, a ruch powrotny pochodzi z tego samego celu.

Wszystkie LB obsługują **symetryczną** lepkość przepływu.

## Sticky session

[źródło](https://www.imperva.com/learn/availability/sticky-session-persistence-and-cookies/)

Dostępne dla: [[#Application Load Balancer|ALB]], [[#Network Load Balancer|NLB]], [[#Classic Load Balancer|CLB]].

Sticky sessions są mechanizmem służącym do kierowania żądań od tego samego klienta do tego samego celu. Przyklejenie (**stickiness**) jest definiowane na poziomie [[#Target group]].

### Sesja bazująca na cookies

Dostępne dla: [[#Application Load Balancer|ALB]], [[#Classic Load Balancer|CLB]].

Są 2 typy syssji opierających się o cookies:

- **duration-based cookies**,
- **application-based cookies**.

#### Uwagi

- W przypadku plików cookie opartych na **aplikacji** nazwy plików cookie muszą być określone indywidualnie dla każdej [[#Target group]]. Nazwy nie mogą być: `AWSSAM`, `AWSALBAPP`, ani `WSALBTG`. ``

- Jednak w przypadku plików cookie opartych na **czasie trwania**, jedyne nazwy używane we wszystkich [[#Target group]] to:
  - `AWSALB` dla [[#Application Load Balancer]],
  - `AWSELB` dla [[#Classic Load Balancer]]

- Jeśli używasz wielu warstw [[#Application Load Balancers]], możesz włączyć lepkie sesje we wszystkich warstwach za pomocą plików cookie opartych na **aplikacji**.

- Jednak w przypadku plików cookie opartych na **czasie trwania** można włączyć lepkie sesje tylko na jednej warstwie, ponieważ `AWSALB` jest jedyną dostępną nazwą.

- Lepkość oparta na **aplikacji** nie działa w przypadku ważonych [[#Target group]].

- Jeśli masz akcję forward z wieloma [[#Target group]], a lepkie sesje są włączone dla jednej lub więcej grup, musisz włączyć lepkość na poziomie [[#Target group]].

#### Duration-based cookies

Gdy trwałość sesji oparta jest na czasie trwania, load balancer wydaje ciasteczko, które definiuje określone ramy czasowe dla trwałości sesji. Za każdym razem, gdy load balancer otrzymuje żądanie klienta, sprawdza czy ten plik cookie jest obecny.

Po upłynięciu określonego czasu i wygaśnięciu cookie, sesja nie jest już lepka.

#### Application-based cookies

Gdy trwałość sesji kontrolowana przez aplikację, to aplikacja generuje plik cookie, który określa czas utrzymywania sesji. Load balancer nadal wystawia swoje własne ciasteczko sesyjne na wierzchu, ale teraz podąża za czasem życia ciasteczka aplikacji.

To sprawia, że lepkie sesje są bardziej wydajne, zapewniając, że użytkownicy nigdy nie są kierowani do serwera po tym, jak ich lokalne ciasteczko sesji już wygasło.

> Jest to jednak bardziej skomplikowane w implementacji, ponieważ wymaga dodatkowej integracji pomiędzy load balancerem a aplikacją.

### Sesja NLB

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-target-groups.html#sticky-sessions)

Dostępne dla: [[#Network Load Balancer|NLB]].

Używanie sesji lepkich może prowadzić do nierównomiernej dystrybucji połączeń i przepływów, co może mieć wpływ na dostępność celów. Na przykład, wszyscy klienci znajdujący się za tym samym [[NAT devices|urządzeniem NAT]] mają ten sam źródłowy adres IP. Dlatego cały ruch z tych klientów jest kierowany do tego samego celu.

Load balancer może zresetować lepkie sesje dla [[#Target group]], jeśli stan zdrowia któregokolwiek z jej celów ulegnie zmianie lub jeśli zarejestrujesz lub wyrejestrujesz cele z [[#Target group]].

Lepkie sesje nie są obsługiwane przez [[#Listener]] TLS i [[#Target group]] TLS.

### Zalety i wady sticky session

Sticky sessions oferują wiele korzyści, które mogą poprawić wydajność aplikacji internetowej, w tym:

- **minimalizacja wymiany danych** - serwery w sieci nie muszą wymieniać danych sesji, co jest kosztownym procesem, gdy odbywa się na dużą skalę;
- **wykorzystanie RAM** - sticky session pozwalają na bardziej efektywne wykorzystanie RAM aplikacji, co przekłada się na lepszą responsywność.

Jednak sticky session utrudniają również utrzymanie równowagi na serwerach. Serwer może zostać przeciążony, jeśli zgromadzi zbyt wiele sesji, lub jeśli określone sesje wymagają dużej ilości zasobów. Może to spowodować, że load balancer będzie musiał przenieść klienta na inny serwer w połowie sesji, co spowoduje utratę danych.

## Deregistration Delay

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#deregistration-delay)

Dostępne dla: [[#Application Load Balancer|ALB]], [[#Network Load Balancer|NLB]], [[#Gateway Load Balancer|GWLB]], [[#Classic Load Balancer|CLB]].

Elastic Load Balancing przestaje wysyłać żądania do celów, które się wyrejestrowują. Domyślnie Elastic Load Balancing czeka 300 sekund przed zakończeniem procesu wyrejestrowania, co może pomóc w ukończeniu trwających żądań do celu. Dostępny zakres to **1s - 1h**.

Początkowy stan wyrejestrowującego się celu jest `draining`. Po upływie opóźnienia wyrejestrowania, proces wyrejestrowania kończy się, a stan celu jest nieużywany. Jeśli cel jest częścią [[AWS Auto Scaling#Auto Scaling group|ASG]], może zostać zakończony i zastąpiony.

Jeśli wyrejestrowujący się cel nie ma trwających żądań i żadnych aktywnych połączeń, Elastic Load Balancing natychmiast kończy proces wyrejestrowywania, nie czekając na upłynięcie opóźnienia wyrejestrowania. Jednakże, nawet jeśli wyrejestrowanie celu jest zakończone, status celu jest wyświetlany jako opróżniający się do czasu wygaśnięcia limitu czasu opóźnienia wyrejestrowania. Po upływie tego czasu, cel przechodzi do stanu nieużywanego.

Jeśli wyrejestrowujący się cel zakończy połączenie przed upływem czasu opóźnienia wyrejestrowania, klient otrzyma odpowiedź o błędzie na poziomie 500.

## Connection Draining

`deprecated`

Dostępne dla: [[#Classic Load Balancer|CLB]].

Tak nazywa się funkjonalność [[#Deregistration Delay]] dla [[#Classic Load Balancer|CLB]].

---

# See also

- [[AWS Auto Scaling]]
- [[EC2 Auto Scaling]]
