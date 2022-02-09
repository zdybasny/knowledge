#AWS #network

Route 53 to wysoce dostępna i skalowalna usługa sieciowa ([Authoritative nameserver](../Network/DNS.md#Authoritative%20nameserver)) systemu nazw domen ([DNS](../Network/DNS.md)) w chmurze. Route 53 jest również w pełni zgodny z IPv6.

Możesz używać Route 53 do wykonywania trzech głównych funkcji w dowolnej kombinacji:

- [Rejestracji domen](#Rejestracji%20domen) (jako [DNS Registrar](../Network/DNS.md#Domain%20Name%20Registrar))
- [Routing DNS](#Routing%20DNS),
- sprawdzania kondycji zasobów ([Health Check](#Health%20Check)).

##### Spis treści

- [Rrejestracja domen](#Rrejestracja%20domen)
- [Routing DNS](#Routing%20DNS)
  - [Hosted zone](#Hosted%20zone)
  - [Obsługiwane rekordy DNS](#Obsługiwane%20rekordy%20DNS)
    - [Alias](#Alias)
      - [Alias vs CNAME](#Alias%20vs%20CNAME)
      - [Obsługiwane zasoby AWS](#Obsługiwane%20zasoby%20AWS)
  - [Routing policy](#Routing%20policy)
    - [Typy polityk routingu](#Typy%20polityk%20routingu)
      - [Simple](#Simple)
      - [Multivalue answer](#Multivalue%20answer)
      - [Latency-based](#Latency-based)
      - [Weighted](#Weighted)
      - [Failover](#Failover)
      - [Geolocation](#Geolocation)
      - [Geoproximity](#Geoproximity)
    - [Kojarzenie Health Checków z rekordami Route 53](#Kojarzenie%20Health%20Checków%20z%20rekordami%20Route%2053)
  - [Traffic flow](#Traffic%20flow)
- [Health Check](#Health%20Check)
- [See also](#See%20also)

# Rrejestracja domen

**Reoute 53** pełni m.in. funkcję  [rejestratora domen](../Network/DNS.md#Domain%20Name%20Registrar), umożliwiając rejestrację nowej [domeny](../Network/DNS.md#Domain%20Name) dostępnej w Internecie. Pozwala także przenieść rejestrację już instniejących domen z innych rejestratorów do Route 53.

Lista dostępnych [TLD](../Network/DNS.md#Top%20Level%20Domain) w Route 53 znajduje się tu: [Index to supported top-level domains](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/registrar-tld-list.html)

Jeśli domena pozostaje zarejestrowana u [rejestratora](../Network/DNS.md#Domain%20Name%20Registrar) zewnętrznego (zazwyczaj płacąc z góry za rok), taki rejestrator zazwyczaj zapewnia usługę DNS do zarządzania [rekordami DNS](../Network/DNS.md#DNS%20Record). Jendak nadal można użyć **Route 53** w tym celu. Dokładniej opisane jest w to w [Routing DNS](#Routing%20DNS).

# Routing DNS

Route 53 może być używany jako usługa DNS dla domeny zarejestrowanej przez Route 53 czy też przez zewnęcznego [rejestratora](../Network/DNS.md#Domain%20Name%20Registrar). Route 53 kieruje ruch internetowy do wybranej witryny, tłumacząc przyjazne [FQDN](../Network/DNS.md#Fully%20Qualified%20Domain%20Name) na numeryczne IP.

W ramach routingu Route 53 łączy żądania użytkowników do infrastruktury działającej w AWS, takiej jak:

- instancje [EC2](EC2.md),
- balansery obciążenia [Elastic Load Balancing](Elastic%20Load%20Balancing.md)
- kubełki (_bucket_) Amazon [S3](S3.md)

Może być również używany do kierowania użytkowników do infrastruktury poza AWS.
Możesz wykorzystać Route 53 do skonfigurowania kontroli stanu DNS, a następnie stale monitorować zdolność aplikacji do odzyskiwania danych po awariach i kontrolować odzyskiwanie aplikacji za pomocą Route 53 [Route 53 Application Recovery Controller](Route%2053%20Application%20Recovery%20Controller.md).

## Hosted zone

[źródło](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-working-with.html)

Strefa hostowana ([DNS zone](../Network/DNS.md#DNS%20zone)) i odpowiadająca jej [domena](../Network/DNS.md#Domain%20Name) mają tę samą nazwę.

Istnieją dwa rodzaje stref hostowanych:

- **publiczna** - zawiera [rekordy](../Network/DNS.md#DNS%20Record) określające sposób kierowania ruchu w Internecie (np. example.com),
- **prywatna** - zawiera [rekordy](../Network/DNS.md#DNS%20Record) określające sposób kierowania ruchu w Amazon [VPC](VPC.md) (np. company.internal).

## Obsługiwane rekordy DNS

Route 53 obsługuje następujące typy [rekordów DNS](../Network/DNS.md#DNS%20Record):
(podstawowe)

- **A**
- **AAAA**
- **CNAME**
- **NS**
- **SOA**

(zaawansowane)

- CAA
- DS
- MX
- NAPTR
- PTR
- TXT
- SPF
- SRV

> Każdy z rekordów posiada wartość [TTL](../Network/DNS.md#time-to-live). Krótszy time-to-live oznacza częstrze odświerzanie rekordów, lecz wąże się to z większym ruchem na Route 53 i, co za tym idzie, większym kosztem.

### Alias

[źródło](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html)

**Rekordy aliasowe** Route 53 stanowią rozszerzenie funkcjonalności DNS. **Rekordy aliasowe** pozwalają kierować ruch do [wybranych zasobów AWS](#Obsługiwane%20zasoby%20AWS) (wtedy mają typ [A](../Network/DNS.md#A) lub [AAAA](../Network/DNS.md#AAAA)). Pozwalają one również na kierowanie ruchu z jednego rekordu w [Hosted zone](#Hosted%20zone) do innego rekordu.

#### Alias vs CNAME

|                                                                  |                                                   Alias                                                  |                   CNAME                   |
| :--------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------: | :---------------------------------------: |
| zasoby celu                                                      | [Obsługiwane zasoby AWS](#Obsługiwane%20zasoby%20AWS), rekordy w tej samej [Hosted zone](#Hosted%20zone) |                rekordy DNS                |
| rekordy o tej samej nazwie co wierzchołek strefy (**zone apex**) |                                    **tak** (o tym samym typie rekordu)                                   |                     -                     |
| koszty                                                           |                                      brak dla zapytań do zasobów AWS                                     |                  **tak**                  |
| odpowiedź na określony typ rekordu w zapytaniu DNS               |                    tylko gdy nazwa i typ aliasu zgadza się z nazwą i typem w zapytaniu                   | niezależnie od typy, gdy nazwa się zgadza |
| listowanie w zapytaniach `dig` lub `nslookup`                    |            jako typ określony w rekordzie (typ zależy od zasobu, do którego kieruje się ruch)            |                 jako CNAME                |
| natywny health check                                             |                                                  **tak**                                                 |                     -                     |
| TTL                                                              |                         **tak** (taki sam jak na zasobie do którego kieruje ruch)                        |                  **tak**                  |

#### Obsługiwane zasoby AWS

**Rekord aliasowy** może kierować ruch do następujacych zasobów AWS:

- rekordy **Route 53** w tej samej [Hosted zone](#Hosted%20zone),
- [Elastic Load Balancing](Elastic%20Load%20Balancing.md),
- dystrybucje [CloudFront](CloudFront.md),
- [API Gateway](API%20Gateway.md),
- [środowika Elastic Beanstalk](Elastic%20Beanstalk.md#Environment),
- strony internetowe na [S3](S3.md),
- [endpointy VPC](VPC.md#VPC%20Endpoint),
- [Global Accelerator](Global%20Accelerator.md).

**Nie można** natomiast ustawić **Aliasu** dla [nazwy DNS](../Network/DNS.md#Domain%20Name) **EC2**!

## Routing policy

[źródło](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html)

Podczas tworzenia rekordu wybieramy **politykę routingu**, która określa w jaki sposób Amazon Route 53 odpowiada na zapytania.

> Nie routuje ruchu tak, tak to robi [Elastic Load Balancing](Elastic%20Load%20Balancing.md)! Routing tu oznacza odpowiedź na zapytania DNS.

### Typy polityk routingu

Dostępne są następujące typy polityk:

- routing **prosty** (**[Simple](#Simple)**) - dla pojedynczego zasobu, który wykonuje określoną funkcję dla domeny (np. serwera WWW);
- routing **wielowartościowy** (**[Multivalue answer](#Multivalue%20answer)**) - aby Route 53 odpowiadał na zapytania DNS z maksymalnie ośmioma zdrowymi rekordami wybranymi losowo;
- routing **oparty na opóźnieniach** (**[Latency-based](#Latency-based)**) - dla zasobów w wielu [Regionach AWS](AWS%20locations.md#Region) aby kierować ruch do Regionu, który zapewnia najlepsze opóźnienia z krótszym czasem round-trip;
- routing **ważony** (**[Weighted](#Weighted)**) - kieruje ruch do wielu zasobów w zdefiniowanych  proporcjach;
- routing **z awaryjnym przełączaniem** (**[Failover](#Failover)**) - konfiguruje active-passive failover;
- routing **geolokalizacyjny** (**[Geolocation](#Geolocation)**) - kieruje ruch w oparciu o lokalizację użytkowników;
- routing **geoproximity** (**[Geoproximity](#Geoproximity)**) - kieruje ruch w oparciu o lokalizację zasobów i opcjonalnie przesuwać ruch z zasobów w jednej lokalizacji do zasobów w innej;

#### Simple

Pozwala na skonfigurowanie standardowych [rekordów DNS](../Network/DNS.md#DNS%20Record), bez specjalnych funkcji routingu Route 53, takich jak ważenie czy opóźnienia. **Routing prosty** przeważnie kieruje ruch do pojedynczego zasobu, np. do serwera WWW dla twojej strony.

Dla **routingu prostego** nie można tworzyć wielu rekordów o tej samej nazwie i typie, ale można w jednym rekordzie podać wiele wartości, np. wiele adresów IP. W takim przypadku Route 53 zwróci wszystkie wartości w losowej kolejności, a [klient](../Network/DNS.md#DNS%20Resolver) losowo wybiera jedną wartość.

Polityka prosta dla rekordu aliasu umożliwia określenie tylko jedenego zasóbu AWS lub jeden rekord w tej samej bieżącej [Hosted zone](#Hosted%20zone).

Prosta politykanie **nie** może byż skojażona z [Health Check](#Health%20Check)'iem.

#### Multivalue answer

**Multiwalue answer routing** pozwala skonfigurować **Route 53** tak, aby w odpowiedzi na zapytania DNS zwracał wiele wartości, takich jak adresy IP serwerów [WWW](http://WWW).

Możesz określić wiele wartości dla niemal każdego rekordu, ale **routing wielowartościowy** pozwala także na sprawdzanie stanu każdego zasobu, dzięki czemu **Route 53** zwraca tylko wartości dla zdrowych zasobów. Route 53 odpowiada na zapytania DNS z maksymalnie ośmioma zdrowymi rekordami i udziela różnych odpowiedzi różnym [DNS resolvers](../Network/DNS.md#DNS%20Resolver).

W przeciwieństwie do [routingu prostego](#Simple) można opcjonalnie skojażyć [Health Check](#Health%20Check). Jeśli żaden [Health Check](#Health%20Check) nie zostanie skojażony, **Route 53** uznaje zawsze taki rekord za zdrowy.

#### Latency-based

Aby korzystać z routingu opartego na opóźnieniach, rekordy muszą być utworzone w wielu [Regionach AWS](AWS%20locations.md#Region).
Kiedy Route 53 otrzymuje zapytanie DNS dla domeny lub subdomeny, sprawdza który region daje użytkownikowi najniższe opóźnienie, a następnie odpowiada wartością z rekordu dla Regionu z najmniejszym opóźnieniem.

#### Weighted

**Routing ważony** pozwala powiązać wiele zasobów z pojedynczą [nazwą domeny](../Network/DNS.md#Domain%20Name) lub subdomeny i wybrać, jaka ilość ruchu jest kierowana do każdego z nich. Nadaje się do balansowania obciążeniem.

**Routing ważony** wymaga, aby każdy [rekord](../Network/DNS.md#DNS%20Record) miał tą samą nazwę i typ dla każdego z zasobów. Każdy taki rekord ma przypisaną względną wagę, w oparciu której Route 53 wysyła ruch do zasobu jako część całkowitej wagi dla wszystkich rekordów w grupie:

![](attachments/Route53%20policy%20weighted.png)

Prosta ważona **może** byż skojażona z [Health Check](#Health%20Check)'iem.

#### Failover

Dla wielu zasobów, które pełnią tę samą funkcję, można skonfigurować **DNS failover** tak, aby Route 53 kierował ruch z niezdrowego zasobu do zdrowego. Za pomocą [Route 53 Application Recovery Controller](Route%2053%20Application%20Recovery%20Controller.md), można skonfigurować [Health Check](#Health%20Check)i routingu z **rekordami DNS failover**, aby zarządzać awaryjnym przełączaniem ruchu dla aplikacji.

#### Geolocation

**Routing geolokalizacyjny** pozwala na wybór zasobów obsługujących ruch w oparciu o geograficzną lokalizację użytkowników, czyli lokalizację, z której pochodzą zapytania DNS. Np. wszystkie zapytania z Europy mają być kierowane do load balancera [ELB](Elastic%20Load%20Balancing.md) w [Regionie](AWS%20locations.md#Region) Frankfurtu.

Można określić lokalizacje geograficzne według kontynentu, kraju lub stanu w Stanach Zjednoczonych.

Jeśli utworzysz osobne rekordy dla nakładających się regionów geograficznych - na przykład jeden rekord dla Ameryki Północnej i jeden dla Kanady - priorytet ma najmniejszy region geograficzny. Dzięki temu można kierować część zapytań dotyczących jednego kontynentu do jednego zasobu, a zapytania dotyczące wybranych krajów na tym kontynencie do innego zasobu.

> Kiedy używasz routingu geolokalizacyjnego, możesz zlokalizować swoją zawartość i zaprezentować część lub całość swojej witryny w języku użytkowników. Możesz również użyć routingu geolokalizacyjnego, aby ograniczyć dystrybucję treści tylko do lokalizacji, w których masz prawa do dystrybucji. Innym możliwym zastosowaniem jest równoważenie obciążenia pomiędzy punktami końcowymi w przewidywalny, łatwy do zarządzania sposób, tak aby każda lokalizacja użytkownika była konsekwentnie kierowana do tego samego punktu końcowego.

Można utworzyć **domyślny rekord**, który będzie obsługiwał zarówno zapytania z adresów IP, które nie są mapowane do żadnej lokalizacji, jak i zapytania pochodzące z lokalizacji, dla których nie utworzono rekordów geolokalizacyjnych. Jeśli nie utworzono **rekordu domyślnego**, Route 53 zwróci odpowiedź `no answer` dla zapytań z tych lokalizacji.

#### Geoproximity

Geoproximity routing (tylko z [Traffic flow](#Traffic%20flow))
**Routing geoproximity** pozwala kierować ruch do zasobów na podstawie geograficznej lokalizacji użytkowników i zasobów.

Można również opcjonalnie wybrać kierowanie większego lub mniejszego ruchu do danego zasobu poprzez określenie wartości, zwanej stonniczością (**bias**). Wartość **bias** rozszerza lub zmniejsza rozmiar regionu geograficznego, z którego kierowany jest ruch do zasobu. Zakres tej wartości to od **-99** (najmniejszy rozmiar) do **99** (największy rozmiar).

Konfiguracja **routing geoproximity** możliwa jest tylko z użyciem [Traffic flow](#Traffic%20flow).

Tworząc reguły **geoproximity** można wybrać ustalić :

- [Region](AWS%20locations.md#Region) AWS dla zasobów AWS,
- szerokość i długość geograficzna dla zasobu non-AWS.

### Kojarzenie Health Checków z rekordami Route 53

| Typ polityki routingu rekordu Route 53    | Wymagany | Opcjonalny | Niedostępny |
| :---------------------------------------- | :------: | :--------: | :---------: |
| [Simple](#Simple)                         |          |            |      X      |
| [Multivalue answer](#Multivalue%20answer) |          |      X     |             |
| [Latency-based](#Latency-based)           |          |      X     |             |
| [Weighted](#Weighted)                     |          |      X     |             |
| [Failover](#Failover)                     |  primary |  secondary |             |
| [Geolocation](#Geolocation)               |          |      X     |             |
| [Geoproximity](#Geoproximity)             |          |      X     |             |

## Traffic flow

[źródło](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/traffic-flow.html) [źródło](https://geeks.wego.com/multi-region-traffic-engineering/)

Można tworzyć rekordy pojedynczo, ale trudno jest śledzić zależności między nimi, gdy przegląda się ustawienia w konsoli Route 53.

**Traffic flow** znacznie upraszcza proces tworzenia i utrzymywania rekordów w dużych i złożonych konfiguracjach dzięku swoim funkcjonalnościom:

- edytor wizualny, (w tym podgląd mapy),
- wersjonowanie,
- automatyczne tworzenie i aktualizowanie rekordów,
- ronowne wykorzystanie dla wielu rekordów w różnych strefach hostingu;

![](attachments/Route%2053%20Traffic%20flow.png)

# Health Check

**Kontrole stanu** Route 53 monitorują stan i wydajność aplikacji internetowych, serwerów WWW i innych zasobów.

Każda utworzona kontrola stanu może monitorować jeden z poniższych elementów:

- health check określonego zasobu (np. serwer WWW),
- health check innych health checków,
- stan [alarmu CloudWatch](CloudWatch.md#Alarm).

> **Health Check** HTTP jest możliwy tylko dla publicznie dostępnych zasobów.

Można również łączyć rezultaty z wielu Health Checków z użyciem:

- **AND**,
- **OR**,
- **NOT**.

---

# See also

- [DNS](../Network/DNS.md)
