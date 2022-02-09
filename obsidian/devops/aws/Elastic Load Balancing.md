#AWS #network

Jednym z aspektów skalowalności (elastycznoći) jest sposób, w jaki kierujemy użytkowników do odpowiedniej infrastruktury, za co odpowiedzialny jest Elastic Load Balancing. ELB umożliwia dystrybucję ruchu do wielu miejsc docelowych, np. oparciu o obciążenie każdego z dostępnych serwerów. Obsługuje jedną lub więcej [Availaility zone](AWS%20locations.md#Availability%20zone)s w ramach [regionu](AWS%20locations.md#Region).

Domyślnie integruje się z [EC2](EC2.md), [Elastic Container Services](Elastic%20Container%20Services.md).

> Load balancer działa jako pojedynczy punkt kontaktu dla całego ruchu przychodzącego z sieci do [Auto Scaling group](EC2.md#Auto%20Scaling%20group). Chociaż Elastic Load Balancing i [EC2 Auto Scaling](EC2.md#EC2%20Auto%20Scaling) są oddzielnymi usługami, współpracują one ze sobą, aby zapewnić wysoką wydajność i dostępność aplikacji działających w Amazon EC2.

##### Spis treści

- [Typy Load Balancerów](#typy-load-balancerów)
  - [Porównanie typów](#porównanie-typów)
  - [Application Load Balancer](#application-load-balancer)
    - [Algorytm ALB](#algorytm-alb)
    - [Komponenty i właściwośći ALB](#komponenty-i-właściwośći-alb)
    - [Podsieci i strefy dla ALB](#podsieci-i-strefy-dla-alb)
      - [Availability Zone](#availability-zone)
      - [Local Zone](#local-zone)
  - [Network Load Balancer](#network-load-balancer)
    - [Algorytm NLB](#algorytm-nlb)
    - [Komponenty i właściwośći NLB](#komponenty-i-właściwośći-nlb)
  - [Gateway Load Balancer](#gateway-load-balancer)
    - [Komponenty i właściwośći GWLB](#komponenty-i-właściwośći-gwlb)
  - [Classic Load Balancer](#classic-load-balancer)
    - [Komponenty i właściwośći CLB](#komponenty-i-właściwośći-clb)
    - [EC2-Classic](#ec2-classic)
- [Komponenty Load Balancerów](#komponenty-load-balancerów)
  - [Security group](#security-group)
  - [Listener](#listener)
    - [Rule](#rule)
  - [Certyfikaty SSL dla ELB](#certyfikaty-ssl-dla-elb)
    - [Tworzenie certyfikatu](#tworzenie-certyfikatu)
  - [Target group](#target-group)
    - [Typy target group](#typy-target-group)
      - [Intanceses vs IP addresses](#intanceses-vs-ip-addresses)
    - [Auto Scaling Group](#auto-scaling-group)
  - [Cross-zone load balancing](#cross-zone-load-balancing)
  - [Zonal isolation](#zonal-isolation)
  - [Health Check](#health-check)
  - [Slow start](#slow-start)
  - [Flow Stickiness](#flow-stickiness)
  - [Sticky session](#sticky-session)
    - [Sesja bazująca na cookies](#sesja-bazująca-na-cookies)
      - [Uwagi](#uwagi)
      - [Duration-based cookies](#duration-based-cookies)
      - [Application-based cookies](#application-based-cookies)
    - [Sesja NLB](#sesja-nlb)
    - [Zalety i wady sticky session](#zalety-i-wady-sticky-session)
  - [Deregistration Delay](#deregistration-delay)
  - [Connection Draining](#connection-draining)
- [See also](#see-also)

# Typy Load Balancerów

Istnieją 4 rodzaje load balancerów:

- [Application Load Balancer](#Application%20Load%20Balancer) (ALB) - do trasowania ruchu HTTP/HTTPS (lub [warstwy 7](../network/Model%20OSI.md#Layer%207))

- [Network Load Balancer](#Network%20Load%20Balancer) (ELB) - do trasowania ruchu TCP lub UDP (lub [warstwy 4](../network/Model%20OSI.md#Layer%204))

- [Gateway Load Balancer](#Gateway%20Load%20Balancer) (GWLB) - do trasowania ruchu TCP

- [Classic Load Balancer](#Classic%20Load%20Balancer) (CLB)  `deprecated` - podstawowe LB pomiędzy [EC2](EC2.md)

## Porównanie typów

[źródło](https://aws.amazon.com/elasticloadbalancing/features/?nc=sn&loc=2)

|                                                       | Application Load Balancer |            Network Load Balancer            |                         Gateway Load Balancer                        |             Classic Load Balancer             |
| :---------------------------------------------------- | :-----------------------: | :-----------------------------------------: | :------------------------------------------------------------------: | :-------------------------------------------: |
| **[Model OSI](../network/Model%20OSI.md) layers**                              |   [Layer 7](../network/Model%20OSI.md#Layer%207)   |            [Layer 4](../network/Model%20OSI.md#Layer%204)            | [Layer 3](../network/Model%20OSI.md#Layer%203) Gateway + [Layer 4](../network/Model%20OSI.md#Layer%204) Load Balancing | [Layer 4](../network/Model%20OSI.md#Layer%204) / [Layer 7](../network/Model%20OSI.md#Layer%207) |
| **[Listener](#Listener)'s rule protocols**                    |     HTTP, HTTPS, gRPC     |                TCP, UDP, TLS                |                                  IP                                  |           TCP, SSL/TLS, HTTP, HTTPS           |
| **[Target group](#Target%20group) type**                            |  IP, [EC2](EC2.md), [Lambda](Lambda.md)  | IP, [EC2](EC2.md), [Application Load Balancer](#Application%20Load%20Balancer) |                              IP, [EC2](EC2.md)                             |       [EC2](EC2.md) (**no TG, directly only**)      |
|                                                       |                           |                                             |                                                                      |                                               |
| **ECS hostowane na EC2**                              |             V             |                      V                      |                                                                      |                       V                       |
| **ECS hostowane na Fargate**                          |             V             |                      V                      |                                                                      |                                               |
|                                                       |                           |                                             |                                                                      |                                               |
| [Health Check](#Health%20Check)                                     |             V             |                      V                      |                                   V                                  |                       V                       |
| [Security group](#Security%20group)                                   |             v             |                                             |                                                                      |                       v                       |
| Outpost support                                       |             V             |                                             |                                                                      |                                               |
| [Local Zone](#Local%20Zone)                                       |             V             |                                             |                                                                      |                                               |
| IP address - Static, Elastic                          |                           |                      V                      |                                                                      |                                               |
| Slow start                                            |             V             |                                             |                                                                      |                                               |
| Connection draining ([Deregistration Delay](#Deregistration%20Delay))       |             V             |                      V                      |                                   V                                  |                       V                       |
| Configurable idle connection timeout                  |             V             |                                             |                                                                      |                                               |
| PrivateLink Support                                   |                           |                   TCP, TLS                  |                                 GWLBE                                |                                               |
| [Zonal isolation](#Zonal%20isolation)                                  |                           |                      V                      |                                   V                                  |                                               |
| Session resumption                                    |             V             |                      V                      |                                                                      |                                               |
| [Flow Stickiness](#Flow%20Stickiness)                                  |         Symmetric         |                  Symmetric                  |                               Symmetric                              |                   Symmetric                   |
| [Sticky session](#Sticky%20session)                                   |             V             |                      V                      |                                                                      |                       V                       |
| Long-lived TCP connection                             |                           |                      V                      |                                   V                                  |                                               |
| Load Balancing to multiple ports on the same instance |             V             |                      V                      |                                   V                                  |                                               |
| Load Balancer deletion protection                     |             V             |                      V                      |                                   V                                  |                                               |
| Preserve Source IP address                            |             V             |                      V                      |                                   V                                  |                                               |
| WebSockets                                            |             V             |                      V                      |                                   V                                  |                                               |
| Supported network/Platforms                           |          [VPC](VPC.md)          |                   [VPC](VPC.md)                   |                                [VPC](VPC.md)                               |           [EC2-Classic](#EC2-Classic), [VPC](VPC.md)           |
| [Cross-zone load balancing](#Cross-zone%20load%20balancing)                        |             V             |                      V                      |                                   V                                  |                       V                       |
| IAM Permissions (Resource, Tag based)                 |             V             |                      V                      |                                   V                                  |              Only resource based              |

## Application Load Balancer

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)

**Application Load Balancer** podejmuje decyzje dotyczące routingu w [warstwie aplikacji](../network/Model%20OSI.md#Layer%207) (HTTP/HTTPS). Obsługuje routing oparty na ścieżkach i zawartości zapytań. Routing jest wykonywany niezależnie dla każdej [Target group](#Target%20group), nawet jeśli cel jest zarejestrowany w wielu grupach docelowych.

Serwery aplikacji nie widzą IP clienta bezpośrednio, tylko adres ALB. Prawdziwy adres klienta dostępny przesyłany jest w nagłówkach zapytań **X-Forwarded-For**, **X-Forwarded-Port**, **X-Forwarded-Proto**,

Nazwa hosta dla ALB jest stała: **XXX.region.elb.amazonaws.com**.

![ALB_NLB.png](ALB_NLB.png.md)

### Algorytm ALB

Można skonfigurować algorytm routingu używany na poziomie [Target group](#Target%20group). Domyślnym algorytmem routingu jest **round robin**; alternatywnie można określić algorytm routingu **najmniej zaległych żądań**.

### Komponenty i właściwośći ALB

- [Security group](#Security%20group)
- [Listener](#Listener)
- [Certyfikaty SSL dla ELB](#Certyfikaty%20SSL%20dla%20ELB)
- [Target group](#Target%20group)
- [Health Check](#Health%20Check)
- [Slow start](#Slow%20start)
- [Flow Stickiness](#Flow%20Stickiness)
- [Sticky session](#Sticky%20session)
- [Cross-zone load balancing](#Cross-zone%20load%20balancing)
- [Deregistration Delay](#Deregistration%20Delay)

### Podsieci i strefy dla ALB

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/application-load-balancers.html#subnets-load-balancer)

Kiedy tworzysz Application Load Balancer, musisz określić jeden z następujących typów podsieci:

- [Availability Zone](#Availability%20Zone),
- [Local Zone](#Local%20Zone),
- Outpost.

#### Availability Zone

Należy wybrać co najmniej dwie [VPC Subnet](VPC%20Subnet.md). Obowiązują następujące ograniczenia:

- Każda [VPC Subnet](VPC%20Subnet.md) musi pochodzić z innej strefy [AZ](AWS%20locations.md#Availability%20zone).

- Aby upewnić się, że Load Balancer może się prawidłowo skalować, sprawdź, czy każda [VPC Subnet](VPC%20Subnet.md) dla Load Balancera ma blok [CIDR](../network/CIDR.md) z co najmniej **/27** bitmaską (na przykład 10.0.0.0/27) i co najmniej 8 wolnych adresów IP na [VPC Subnet](VPC%20Subnet.md). Twój load balancer używa tych adresów IP do nawiązywania połączeń z celami. W zależności od profilu ruchu, Load Balancer może skalować się wyżej i zużywać maksymalnie do 100 adresów IP rozłożonych na wszystkie włączone [VPC Subnet](VPC%20Subnet.md)

#### Local Zone

Można określić jedną lub więcej [VPC Subnet](VPC%20Subnet.md) strefy [lokalnej](AWS%20locations.md#Local%20zone). Obowiązują następujące ograniczenia:

- Nie możesz używać [WAF](WAF.md) z load balancerem.

- Nie można używać funkcji [Lambda](Lambda.md) jako celu.

## Network Load Balancer

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html)

Network Load Balancer funkcjonuje w [warstwie transportu](../network/Model%20OSI.md#Layer%204) OSI. Może obsłużyć miliony żądań na sekundę. Po otrzymaniu przez Load Balancer żądania połączenia, wybiera on cel z [Target group](#Target%20group) dla domyślnej [Rule](#Rule). Próbuje otworzyć połączenie TCP do wybranego celu na porcie określonym w konfiguracji [Listener](#Listener)a.

NLB tworzy interfejs sieciowy dla każdej [AZ](AWS%20locations.md#Availability%20zone), którą włączysz. Każdy węzeł równoważenia obciążenia w strefie dostępności używa tego interfejsu sieciowego, aby uzyskać **statyczny adres IP**. Kiedy tworzysz load balancer wychodzący do Internetu, możesz opcjonalnie przypisać jeden [Elastic IP](Elastic%20IP.md) na [VPC Subnet](VPC%20Subnet.md).

![ALB_NLB.png](ALB_NLB.png.md)

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

- [Listener](#Listener)
- [Certyfikaty SSL dla ELB](#Certyfikaty%20SSL%20dla%20ELB)
- [Target group](#Target%20group)
- [Health Check](#Health%20Check)
- [Flow Stickiness](#Flow%20Stickiness)
- [Sticky session](#Sticky%20session)
- [Cross-zone load balancing](#Cross-zone%20load%20balancing)
- [Zonal isolation](#Zonal%20isolation)
- [Deregistration Delay](#Deregistration%20Delay)

## Gateway Load Balancer

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html)

**GWLB** umożliwia wdrażanie, skalowanie i zarządzanie wirtualnymi urządzeniami, takimi jak zapory sieciowe, systemy wykrywania i zapobiegania włamaniom oraz systemy głębokiej inspekcji pakietów. Łączy w sobie przejrzystą [Internet Gateway](Internet%20Gateway.md) (czyli pojedynczy punkt wejścia i wyjścia dla całego ruchu) i dystrybuuje ruch, jednocześnie skalując urządzenia wirtualne wraz z zapotrzebowaniem.

Gateway Load Balancer działa na [trzeciej warstwie](../network/Model%20OSI.md#Layer%203) (warstwie sieciowej) [modelu OSI](../network/Model%20OSI.md). Nasłuchuje wszystkich pakietów IP na wszystkich portach i przekazuje ruch do [Target group](#Target%20group), która jest określona w [regule nasłuchu](#Rule). Utrzymuje [Flow Stickiness](#Flow%20Stickiness) do konkretnego urządzenia docelowego używając 5-tuple (dla przepływów TCP/UDP) lub 3-tuple (dla przepływów innych niż TCP/UDP).

Gateway Load Balancer i jego zarejestrowane instancje wirtualnych urządzeń wymieniają ruch aplikacyjny za pomocą protokołu GENEVE na porcie 6081. Obsługuje on maksymalny rozmiar jednostki transmisji (MTU) 8500 bajtów.

![GWLB.png](GWLB.png.md)

Gateway Load Balancer używa punktów końcowych (**endpoint**) Gateway Load Balancer do bezpiecznej wymiany ruchu pomiędzy granicami [VPC](VPC.md). Endpoint GWLB jest punktem końcowym [VPC](VPC.md), który zapewnia prywatną łączność między urządzeniami wirtualnymi w [VPC](VPC.md) dostawcy usług a serwerami aplikacji w [VPC](VPC.md) odbiorcy usług. Wdrażasz Gateway Load Balancer w tym samym [VPC](VPC.md) co urządzenia wirtualne. Urządzenia wirtualne są rejestrowane w [Target group](#Target%20group) dla urządzenia Gateway Load Balancer.

Ruch do i z punktu końcowego Gateway Load Balancer jest konfigurowany za pomocą [VPC Route table](VPC%20Route%20table.md). Ruch przepływa z [VPC](VPC.md) odbiorcy usług przez punkt końcowy Gateway Load Balancer do Gateway Load Balancer w [VPC](VPC.md) dostawcy usług, a następnie powraca do VPC odbiorcy usług. Należy utworzyć punkt końcowy Gateway Load Balancer i serwery aplikacji w różnych [VPC Subnet](VPC%20Subnet.md). Dzięki temu można skonfigurować punkt końcowy Gateway Load Balancer jako następny skok w [VPC Route table](VPC%20Route%20table.md) dla [VPC Subnet](VPC%20Subnet.md) aplikacji.

### Komponenty i właściwośći GWLB

- [Listener](#Listener)
- [Target group](#Target%20group)
- [Health Check](#Health%20Check)
- [Flow Stickiness](#Flow%20Stickiness)
- [Cross-zone load balancing](#Cross-zone%20load%20balancing)
- [Zonal isolation](#Zonal%20isolation)
- [Deregistration Delay](#Deregistration%20Delay)

## Classic Load Balancer

`deprecated`

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/introduction.html)

CLB zapewnia podstawowe równoważenie obciążenia pomiędzy wieloma [EC2](EC2.md) i działa zarówno na poziomie żądania, jak i połączenia. CLB jest przeznaczony dla aplikacji, które są zbudowane w sieci [EC2-Classic](#EC2-Classic).

![CLB.png](CLB.png.md)

Zalecany jest:

- [Application Load Balancer](#Application%20Load%20Balancer) dla ruchu w [warstwie 7](../network/Model%20OSI.md#Layer%207) i
- [Network Load Balancer](#Network%20Load%20Balancer) dla ruchu w [warstwie 4](../network/Model%20OSI.md#Layer%204)

podczas korzystania z [VPC](VPC.md).

Mimo wyraźnego zalecenia używania ALB, CLB może dostarczać kożyśći:

- wspracie dla [EC2-Classic](#EC2-Classic),
- wsparcie dla listenerów TPC i SSL,
- wsparcie dla Sticky Sesstion używających coockies generowanych przez aplikacje.

### Komponenty i właściwośći CLB

- [Security group](#Security%20group)
- [Listener](#Listener)
- [Certyfikaty SSL dla ELB](#Certyfikaty%20SSL%20dla%20ELB)
- [Health Check](#Health%20Check)
- [Flow Stickiness](#Flow%20Stickiness)
- [Sticky session](#Sticky%20session)
- [Cross-zone load balancing](#Cross-zone%20load%20balancing)
- [Connection Draining](#Connection%20Draining)

### EC2-Classic

`deprecated`

Z EC2-Classic, twoje instancje działają w jednej, płaskiej sieci, którą dzielisz z innymi klientami.
Platforma EC2-Classic została wprowadzona w oryginalnym wydaniu Amazon EC2. Jeśli utworzyłeś swoje konto AWS po 2013-12-04, nie obsługuje EC2-Classic, więc musisz uruchomić swoje instancje Amazon EC2 w VPC.

# Komponenty Load Balancerów

## Security group

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-update-security-groups.html)

Dostępne dla: [ALB](#Application%20Load%20Balancer), [CLB](#Classic%20Load%20Balancer).

Musisz upewnić się, że LB może komunikować się z zarejestrowanymi celami zarówno na porcie [Listener](#Listener)a jak i porcie [Health Check](#Health%20Check). Za każdym razem, gdy dodajesz [Listener](#Listener) do LB lub aktualizujesz port [Health Check](#Health%20Check) dla [Target group](#Target%20group) używanej przez LB do [trasowania](../network/NAT.md) żądań, musisz sprawdzić, czy [Security group](Security%20group.md) skojarzone z LB zezwalają na ruch na nowym porcie w obu kierunkach. W [VPC](VPC.md) podajesz [Security group](Security%20group.md) dla load balancera, co pozwala Ci wybrać porty i protokoły, które mają być dozwolone.

## Listener

Dostępne dla: [ALB](#Application%20Load%20Balancer), [NLB](#Network%20Load%20Balancer), [GWLB](#Gateway%20Load%20Balancer), [CLB](#Classic%20Load%20Balancer).

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

#security

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-https-listener.html)

Dostępne dla: [ALB](#Application%20Load%20Balancer), [NLB](#Network%20Load%20Balancer),  [CLB](#Classic%20Load%20Balancer).

Aby użyć [Listener](#Listener) HTTPS, musisz wdrożyć co najmniej jeden certyfikat X.509 (sertyfikat serwera [SSL-TLS](../network/SSL-TLS.md)) na swoim load balancerze. Load balancer używa certyfikatu do zakończenia połączenia front-end, a następnie odszyfrowuje żądania od klientów przed wysłaniem ich do celów.

![ELB SSL.png](ELB%20SSL.png.md)

### Tworzenie certyfikatu

- Podczas tworzenia listenera HTTPS należy podać dokładnie jeden certyfikat. Certyfikat ten nazywany jest **certyfikatem domyślnym**. Po utworzeniu listenera HTTPS można zastąpić domyślny certyfikat.

- Podczas tworzenia certyfikatu do użycia z load balancerem należy określić [nazwę domeny](../network/DNS.md).

- Klient może użyć Server Name Indication ([SNI](../network/SSL-TLS.md#Server%20Name%20Indication)), aby nazwę hosta. Dostępne dla: [ALB](#Application%20Load%20Balancer), [NLB](#Network%20Load%20Balancer).

- Można określić dodatkowe certyfikaty na **liście certyfikatów** do obsługi wielu domen. W takim przypadku domyślny certyfikat jest używany tylko wtedy, gdy klient łączy się bez użycia protokołu [SNI](../network/SSL-TLS.md#Server%20Name%20Indication) do określenia nazwy hosta lub gdy na liście certyfikatów nie ma pasujących certyfikatów.

> Zaleca się tworzenie certyfikatów dla load balancera za pomocą [Certificate Manager](Certificate%20Manager.md) (ACM). ACM integruje się z Elastic Load Balancing, dzięki czemu można wdrożyć certyfikat na load balancerze.

> Alternatywnie można użyć narzędzi SSL/TLS do utworzenia żądania podpisania certyfikatu ([CSR](../network/SSL-TLS.md#CSR)), następnie uzyskać podpisanie [CSR](../network/SSL-TLS.md#CSR) przez [CA](../network/SSL-TLS.md#CA) w celu utworzenia certyfikatu, a następnie zaimportować certyfikat do ACM lub przesłać certyfikat do AWS Identity and Access Management ([IAM](IAM.md)).

Jeśli nie określisz dodatkowych certyfikatów, ale musisz hostować wiele bezpiecznych aplikacji za pośrednictwem jednego load balancera, możesz użyć certyfikatu wieloznacznego lub dodać do certyfikatu Subject Alternative Name (SAN) dla każdej dodatkowej domeny.

## Target group

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)

Dostępne dla: [ALB](#Application%20Load%20Balancer), [NLB](#Network%20Load%20Balancer), [GWLB](#Gateway%20Load%20Balancer).

**Target group** kieruje żądania do jednego lub więcej zarejestrowanych celów (np. [EC2](EC2.md)), używając protokołu i portu, który określisz. Możesz zarejestrować jeden cel z wieloma grupami docelowymi.

|                       | Application Load Balancer |            Network Load Balancer            | Gateway Load Balancer | Classic Load Balancer |
| :-------------------- | :-----------------------: | :-----------------------------------------: | :-------------------: | :-------------------: |
| **Target group type** |  IP, [EC2](EC2.md), [Lambda](Lambda.md)  | IP, [EC2](EC2.md), [Application Load Balancer](#Application%20Load%20Balancer) |      IP, [EC2](EC2.md)      |                       |

### Typy target group

**Instances**

- Obsługuje load balancing do [EC2](EC2.md) w ramach określonego [VPC](VPC.md).

**IP addresses**

- Wspiera load balancing do [VPC](VPC.md) i zasobów lokalnych, IP musi być prywatny.
- Ułatwia routing do wielu **adresów IP** i interfejsów sieciowych na tej samej instancji.
- Oferuje elastyczność w architekturach opartych na mikroserwisach, upraszczając komunikację między aplikacjami.

**Lambda function**

- Ułatwia routing do pojedynczej funkcji [Lambda](Lambda.md).

**Application Load Balancer**

- Oferuje elastyczność [NLB](#Network%20Load%20Balancer) w akceptowaniu i trasowaniu zapytań TCP w ramach danego [VPC](VPC.md).
- Ułatwia używanie statycznych IP oraz PrivateLink razem z [ALB](#Application%20Load%20Balancer).

#### Intanceses vs IP addresses

Instances:

- Typ docelowy [EC2](EC2.md) jest ograniczony tylko do niektórych instancji. Powinien to być domyślny wybór podczas load balancingu instancji.
- Na przykład, jeśli masz instancje w [Auto Scaling group](Auto%20Scaling.md#Auto%20Scaling%20group) (ASG), ASG może automatycznie zarejestrować twoje instancje z twoim load balancerem. Nie możesz tego zrobić dla typów docelowych IP.

IP adresses:

- Cel nie musi być instancją - wszystko z prywatnym adresem IP będzie działać, w tym wewnętrzny load balance, prywatna usługa VPC, kontenery Fargate, bazy danych, serwery on-premise przez VPN.
- Cel może być w innym regionie, tak długo, jak masz cross-region peering między swoimi VPC masz wiele interfejsów sieciowych na swojej instancji, więc możesz rozdzielać ruch pomiędzy nimi, np. różne aplikacje na jednej instancji są przypisane do różnych interfejsów.
- Każdy interfejs może być powiązany z inną grupą docelową.

### Auto Scaling Group

#autoscaling

Po dołączeniu [target grupy](#Target%20group) do [Auto Scaling Group](EC2%20Auto%20Scaling.md#Auto%20Scaling%20Group), [EC2 Auto Scaling](EC2%20Auto%20Scaling.md) rejestruje swoje cele z [Target group](Elastic%20Load%20Balancing.md#Target%20group) dla Ciebie, gdy je uruchamia, a [EC2](EC2.md), które są kończone przez Auto Scaling są automatycznie wyrejestrowywane z load balancera.

**Więcej: [EC2 Auto Scaling](EC2%20Auto%20Scaling.md).**

## Cross-zone load balancing

[źródło]()

Dostępne dla: [ALB](#Application%20Load%20Balancer), [NLB](#Network%20Load%20Balancer), [GWLB](#Gateway%20Load%20Balancer), [CLB](#Classic%20Load%20Balancer).

Po włączeniu [Availability zone](AWS%20locations.md#Availability%20zone) dla load balancera, Elastic Load Balancing tworzy węzeł load balancera w [AZ](AWS%20locations.md#Availability%20zone). Domyślnie każdy węzeł balansera obciążenia dystrybuuje ruch pomiędzy zarejestrowanymi celami tylko w swojej  [AZ](AWS%20locations.md#Availability%20zone).

Jeśli włączysz **międzystrefowe równoważenie obciążenia**, każdy węzeł równoważenia obciążenia dystrybuuje ruch między zarejestrowanymi celami we wszystkich włączonych [AZ](AWS%20locations.md#Availability%20zone).

![ELB Cross-Zone LB.png](ELB%20Cross-Zone%20LB.png.md)

## Zonal isolation

[źródło](https://aws.amazon.com/elasticloadbalancing/network-load-balancer/)

Dostępne dla: [NLB](#Network%20Load%20Balancer), [GWLB](#Gateway%20Load%20Balancer).

Chociaż zaleca się skonfigurowanie load balancerów i celów w wielu [AZ](AWS%20locations.md#Availability%20zone) w celu osiągnięcia wysokiej dostępności, Load Balancer może być włączony w pojedynczej [AZ](AWS%20locations.md#Availability%20zone), aby wspierać architektury, które wymagają izolacji strefowej.

## Health Check

Dostępne dla: [ALB](#Application%20Load%20Balancer), [NLB](#Network%20Load%20Balancer), [GWLB](#Gateway%20Load%20Balancer), [CLB](#Classic%20Load%20Balancer).

Elastic Load Balancer kieruje ruch tylko do zdrowych celów, takich jak instancje [EC2](EC2.md), kontenery, adresy IP, mikroserwisy, funkcje [Lambda](Lambda.md) i urządzenia. Health Checki są kluczowe, gdyż informują LB, czy [Target group](#Target%20group)s (lub [EC2](EC2.md) dla [CLB](#Classic%20Load%20Balancer)) do nich podpięte są dostępne.

FSprawdzenie zdrowia jest wykonywane na:

- porcie (**port**) i
- trasie (**route**).

Jeśli odpowiedzią nie jest 200 (OK), to instancja jest niezdrowa (**unhealthy**).

## Slow start

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#slow-start-mode)

Dostępne dla: [ALB](#Application%20Load%20Balancer).

Domyślnie, cel zaczyna otrzymywać swój pełny udział w żądaniach tak szybko, jak tylko zostanie zarejestrowany w [Target group](#Target%20group) i przejdzie [Health Check](#Health%20Check). Użycie trybu powolnego startu daje celom czas na rozgrzanie się zanim load balancer wyśle do nich pełną część żądań.

Po włączeniu powolnego startu dla [Target group](#Target%20group), jej cele wchodzą w tryb powolnego startu, gdy zostaną uznane za zdrowe przez [Target group](#Target%20group). Cel w trybie powolnego startu opuszcza tryb powolnego startu, gdy upłynie skonfigurowany czas trwania powolnego startu lub cel staje się niezdrowy. Load balancer liniowo zwiększa liczbę żądań, które może wysłać do celu w trybie powolnego startu. Po tym jak zdrowy cel opuści tryb powolnego startu, load balancer może wysyłać do niego pełny udział żądań.

## Flow Stickiness

[źródło](https://aws.amazon.com/elasticloadbalancing/features/?nc=sn&loc=2)

Dostępne dla: [ALB](#Application%20Load%20Balancer), [NLB](#Network%20Load%20Balancer), [GWLB](#Gateway%20Load%20Balancer), [CLB](#Classic%20Load%20Balancer).

Wszystkie pakiety przepływu są wysyłane do jednego celu, a ruch powrotny pochodzi z tego samego celu.

Wszystkie LB obsługują **symetryczną** lepkość przepływu.

## Sticky session

[źródło](https://www.imperva.com/learn/availability/sticky-session-persistence-and-cookies/)

Dostępne dla: [ALB](#Application%20Load%20Balancer), [NLB](#Network%20Load%20Balancer), [CLB](#Classic%20Load%20Balancer).

Sticky sessions są mechanizmem służącym do kierowania żądań od tego samego klienta do tego samego celu. Przyklejenie (**stickiness**) jest definiowane na poziomie [Target group](#Target%20group).

### Sesja bazująca na cookies

Dostępne dla: [ALB](#Application%20Load%20Balancer), [CLB](#Classic%20Load%20Balancer).

Są 2 typy syssji opierających się o cookies:

- **duration-based cookies**,
- **application-based cookies**.

#### Uwagi

- W przypadku plików cookie opartych na **aplikacji** nazwy plików cookie muszą być określone indywidualnie dla każdej [Target group](#Target%20group). Nazwy nie mogą być: `AWSSAM`, `AWSALBAPP`, ani `WSALBTG`. ``

- Jednak w przypadku plików cookie opartych na **czasie trwania**, jedyne nazwy używane we wszystkich [Target group](#Target%20group) to:
  - `AWSALB` dla [Application Load Balancer](#Application%20Load%20Balancer),
  - `AWSELB` dla [Classic Load Balancer](#Classic%20Load%20Balancer)

- Jeśli używasz wielu warstw [Application Load Balancers](#Application%20Load%20Balancers), możesz włączyć lepkie sesje we wszystkich warstwach za pomocą plików cookie opartych na **aplikacji**.

- Jednak w przypadku plików cookie opartych na **czasie trwania** można włączyć lepkie sesje tylko na jednej warstwie, ponieważ `AWSALB` jest jedyną dostępną nazwą.

- Lepkość oparta na **aplikacji** nie działa w przypadku ważonych [Target group](#Target%20group).

- Jeśli masz akcję forward z wieloma [Target group](#Target%20group), a lepkie sesje są włączone dla jednej lub więcej grup, musisz włączyć lepkość na poziomie [Target group](#Target%20group).

#### Duration-based cookies

Gdy trwałość sesji oparta jest na czasie trwania, load balancer wydaje ciasteczko, które definiuje określone ramy czasowe dla trwałości sesji. Za każdym razem, gdy load balancer otrzymuje żądanie klienta, sprawdza czy ten plik cookie jest obecny.

Po upłynięciu określonego czasu i wygaśnięciu cookie, sesja nie jest już lepka.

#### Application-based cookies

Gdy trwałość sesji kontrolowana przez aplikację, to aplikacja generuje plik cookie, który określa czas utrzymywania sesji. Load balancer nadal wystawia swoje własne ciasteczko sesyjne na wierzchu, ale teraz podąża za czasem życia ciasteczka aplikacji.

To sprawia, że lepkie sesje są bardziej wydajne, zapewniając, że użytkownicy nigdy nie są kierowani do serwera po tym, jak ich lokalne ciasteczko sesji już wygasło.

> Jest to jednak bardziej skomplikowane w implementacji, ponieważ wymaga dodatkowej integracji pomiędzy load balancerem a aplikacją.

### Sesja NLB

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-target-groups.html#sticky-sessions)

Dostępne dla: [NLB](#Network%20Load%20Balancer).

Używanie sesji lepkich może prowadzić do nierównomiernej dystrybucji połączeń i przepływów, co może mieć wpływ na dostępność celów. Na przykład, wszyscy klienci znajdujący się za tym samym [urządzeniem NAT](NAT%20devices.md) mają ten sam źródłowy adres IP. Dlatego cały ruch z tych klientów jest kierowany do tego samego celu.

Load balancer może zresetować lepkie sesje dla [Target group](#Target%20group), jeśli stan zdrowia któregokolwiek z jej celów ulegnie zmianie lub jeśli zarejestrujesz lub wyrejestrujesz cele z [Target group](#Target%20group).

Lepkie sesje nie są obsługiwane przez [Listener](#Listener) TLS i [Target group](#Target%20group) TLS.

### Zalety i wady sticky session

Sticky sessions oferują wiele korzyści, które mogą poprawić wydajność aplikacji internetowej, w tym:

- **minimalizacja wymiany danych** - serwery w sieci nie muszą wymieniać danych sesji, co jest kosztownym procesem, gdy odbywa się na dużą skalę;
- **wykorzystanie RAM** - sticky session pozwalają na bardziej efektywne wykorzystanie RAM aplikacji, co przekłada się na lepszą responsywność.

Jednak sticky session utrudniają również utrzymanie równowagi na serwerach. Serwer może zostać przeciążony, jeśli zgromadzi zbyt wiele sesji, lub jeśli określone sesje wymagają dużej ilości zasobów. Może to spowodować, że load balancer będzie musiał przenieść klienta na inny serwer w połowie sesji, co spowoduje utratę danych.

## Deregistration Delay

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#deregistration-delay)

Dostępne dla: [ALB](#Application%20Load%20Balancer), [NLB](#Network%20Load%20Balancer), [GWLB](#Gateway%20Load%20Balancer), [CLB](#Classic%20Load%20Balancer).

Elastic Load Balancing przestaje wysyłać żądania do celów, które się wyrejestrowują. Domyślnie Elastic Load Balancing czeka 300 sekund przed zakończeniem procesu wyrejestrowania, co może pomóc w ukończeniu trwających żądań do celu. Dostępny zakres to **1s - 1h**.

Początkowy stan wyrejestrowującego się celu jest `draining`. Po upływie opóźnienia wyrejestrowania, proces wyrejestrowania kończy się, a stan celu jest nieużywany. Jeśli cel jest częścią [ASG](Auto%20Scaling.md#Auto%20Scaling%20group), może zostać zakończony i zastąpiony.

Jeśli wyrejestrowujący się cel nie ma trwających żądań i żadnych aktywnych połączeń, Elastic Load Balancing natychmiast kończy proces wyrejestrowywania, nie czekając na upłynięcie opóźnienia wyrejestrowania. Jednakże, nawet jeśli wyrejestrowanie celu jest zakończone, status celu jest wyświetlany jako opróżniający się do czasu wygaśnięcia limitu czasu opóźnienia wyrejestrowania. Po upływie tego czasu, cel przechodzi do stanu nieużywanego.

Jeśli wyrejestrowujący się cel zakończy połączenie przed upływem czasu opóźnienia wyrejestrowania, klient otrzyma odpowiedź o błędzie na poziomie 500.

## Connection Draining

`deprecated`

Dostępne dla: [CLB](#Classic%20Load%20Balancer).

Tak nazywa się funkjonalność [Deregistration Delay](#Deregistration%20Delay) dla [CLB](#Classic%20Load%20Balancer).

---

# See also

- [EC2](EC2.md)
- [Auto Scaling](Auto%20Scaling.md)
- [EC2 Auto Scaling](EC2%20Auto%20Scaling.md)
