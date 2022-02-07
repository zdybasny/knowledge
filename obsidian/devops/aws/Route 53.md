#AWS #network

Route 53 to wysoce dostępna i skalowalna usługa sieciowa ([[DNS#Authoritative nameserver|Authoritative nameserver]]) systemu nazw domen ([[DNS]]) w chmurze. Route 53 jest również w pełni zgodny z IPv6.

Możesz używać Route 53 do wykonywania trzech głównych funkcji w dowolnej kombinacji:

- [[#Rejestracji domen]] (jako [[DNS#Domain Name Registrar|DNS Registrar]])
- [[#Routing DNS]],
- sprawdzania kondycji zasobów ([[#Health Check]]).

##### Spis treści

- [[#Rrejestracja domen]]
- [[#Routing DNS]]
  - [[#Hosted zone]]
  - [[#Obsługiwane rekordy DNS]]
    - [[#Alias]]
      - [[#Alias vs CNAME]]
      - [[#Obsługiwane zasoby AWS]]
  - [[#Routing policy]]
    - [[#Typy polityk routingu]]
      - [[#Simple]]
      - [[#Multivalue answer]]
      - [[#Latency-based]]
      - [[#Weighted]]
      - [[#Failover]]
      - [[#Geolocation]]
      - [[#Geoproximity]]
    - [[#Kojarzenie Health Checków z rekordami Route 53]]
  - [[#Traffic flow]]
- [[#Health Check]]
- [[#See also]]


# Rrejestracja domen

**Reoute 53** pełni m.in. funkcję  [[DNS#Domain Name Registrar|rejestratora domen]], umożliwiając rejestrację nowej [[DNS#Domain Name|domeny]] dostępnej w Internecie. Pozwala także przenieść rejestrację już instniejących domen z innych rejestratorów do Route 53.

Lista dostępnych [[DNS#Top Level Domain|TLD]] w Route 53 znajduje się tu: [Index to supported top-level domains](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/registrar-tld-list.html)

Jeśli domena pozostaje zarejestrowana u [[DNS#Domain Name Registrar|rejestratora]] zewnętrznego (zazwyczaj płacąc z góry za rok), taki rejestrator zazwyczaj zapewnia usługę DNS do zarządzania [[DNS#DNS Record|rekordami DNS]]. Jendak nadal można użyć **Route 53** w tym celu. Dokładniej opisane jest w to w [[#Routing DNS]].

# Routing DNS

Route 53 może być używany jako usługa DNS dla domeny zarejestrowanej przez Route 53 czy też przez zewnęcznego [[DNS#Domain Name Registrar|rejestratora]]. Route 53 kieruje ruch internetowy do wybranej witryny, tłumacząc przyjazne [[DNS#Fully Qualified Domain Name|FQDN]] na numeryczne IP.

W ramach routingu Route 53 łączy żądania użytkowników do infrastruktury działającej w AWS, takiej jak:

- instancje [[EC2]],
- balansery obciążenia [[Elastic Load Balancing]]
- kubełki (_bucket_) Amazon [[S3]]

Może być również używany do kierowania użytkowników do infrastruktury poza AWS.
Możesz wykorzystać Route 53 do skonfigurowania kontroli stanu DNS, a następnie stale monitorować zdolność aplikacji do odzyskiwania danych po awariach i kontrolować odzyskiwanie aplikacji za pomocą Route 53 [[Route 53 Application Recovery Controller]].

## Hosted zone

[źródło](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-working-with.html)

Strefa hostowana ([[DNS#DNS zone|DNS zone]]) i odpowiadająca jej [[DNS#Domain Name|domena]] mają tę samą nazwę.

Istnieją dwa rodzaje stref hostowanych:

- **publiczna** - zawiera [[DNS#DNS Record|rekordy]] określające sposób kierowania ruchu w Internecie (np. example.com),
- **prywatna** - zawiera [[DNS#DNS Record|rekordy]] określające sposób kierowania ruchu w Amazon [[VPC]] (np. company.internal).

## Obsługiwane rekordy DNS

Route 53 obsługuje następujące typy [[DNS#DNS Record|rekordów DNS]]:
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

> Każdy z rekordów posiada wartość [[DNS#time-to-live|TTL]]. Krótszy time-to-live oznacza częstrze odświerzanie rekordów, lecz wąże się to z większym ruchem na Route 53 i, co za tym idzie, większym kosztem.

### Alias

[źródło](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html)

**Rekordy aliasowe** Route 53 stanowią rozszerzenie funkcjonalności DNS. **Rekordy aliasowe** pozwalają kierować ruch do [[#Obsługiwane zasoby AWS|wybranych zasobów AWS]] (wtedy mają typ [[DNS#A|A]] lub [[DNS#AAAA|AAAA]]). Pozwalają one również na kierowanie ruchu z jednego rekordu w [[#Hosted zone]] do innego rekordu.

#### Alias vs CNAME

|                                                                  |                                        Alias                                       |                   CNAME                   |
| :--------------------------------------------------------------- | :--------------------------------------------------------------------------------: | :---------------------------------------: |
| zasoby celu                                                      |          [[#Obsługiwane zasoby AWS]], rekordy w tej samej [[#Hosted zone]]         |                rekordy DNS                |
| rekordy o tej samej nazwie co wierzchołek strefy (**zone apex**) |                         **tak** (o tym samym typie rekordu)                        |                     -                     |
| koszty                                                           |                           brak dla zapytań do zasobów AWS                          |                  **tak**                  |
| odpowiedź na określony typ rekordu w zapytaniu DNS               |         tylko gdy nazwa i typ aliasu zgadza się z nazwą i typem w zapytaniu        | niezależnie od typy, gdy nazwa się zgadza |
| listowanie w zapytaniach `dig` lub `nslookup`                    | jako typ określony w rekordzie (typ zależy od zasobu, do którego kieruje się ruch) |                 jako CNAME                |
| natywny health check                                             |                                       **tak**                                      |                     -                     |
| TTL                                                              |              **tak** (taki sam jak na zasobie do którego kieruje ruch)             |                  **tak**                  |

#### Obsługiwane zasoby AWS

**Rekord aliasowy** może kierować ruch do następujacych zasobów AWS:

- rekordy **Route 53** w tej samej [[#Hosted zone]],
- [[Elastic Load Balancing]],
- dystrybucje [[CloudFront]],
- [[API Gateway]],
- [[Elastic Beanstalk#Environment|środowika Elastic Beanstalk]],
- strony internetowe na [[S3]],
- [[VPC#VPC Endpoint|endpointy VPC]],
- [[Global Accelerator]].

**Nie można** natomiast ustawić **Aliasu** dla [[DNS#Domain Name|nazwy DNS]] **EC2**!

## Routing policy

[źródło](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html)

Podczas tworzenia rekordu wybieramy **politykę routingu**, która określa w jaki sposób Amazon Route 53 odpowiada na zapytania.

> Nie routuje ruchu tak, tak to robi [[Elastic Load Balancing]]! Routing tu oznacza odpowiedź na zapytania DNS.

### Typy polityk routingu

Dostępne są następujące typy polityk:

- routing **prosty** (**[[#Simple]]**) - dla pojedynczego zasobu, który wykonuje określoną funkcję dla domeny (np. serwera WWW);
- routing **wielowartościowy** (**[[#Multivalue answer]]**) - aby Route 53 odpowiadał na zapytania DNS z maksymalnie ośmioma zdrowymi rekordami wybranymi losowo;
- routing **oparty na opóźnieniach** (**[[#Latency-based]]**) - dla zasobów w wielu [[AWS locations#Region|Regionach AWS]] aby kierować ruch do Regionu, który zapewnia najlepsze opóźnienia z krótszym czasem round-trip;
- routing **ważony** (**[[#Weighted]]**) - kieruje ruch do wielu zasobów w zdefiniowanych  proporcjach;
- routing **z awaryjnym przełączaniem** (**[[#Failover]]**) - konfiguruje active-passive failover;
- routing **geolokalizacyjny** (**[[#Geolocation]]**) - kieruje ruch w oparciu o lokalizację użytkowników;
- routing **geoproximity** (**[[#Geoproximity]]**) - kieruje ruch w oparciu o lokalizację zasobów i opcjonalnie przesuwać ruch z zasobów w jednej lokalizacji do zasobów w innej;

#### Simple

Pozwala na skonfigurowanie standardowych [[DNS#DNS Record|rekordów DNS]], bez specjalnych funkcji routingu Route 53, takich jak ważenie czy opóźnienia. **Routing prosty** przeważnie kieruje ruch do pojedynczego zasobu, np. do serwera WWW dla twojej strony.

Dla **routingu prostego** nie można tworzyć wielu rekordów o tej samej nazwie i typie, ale można w jednym rekordzie podać wiele wartości, np. wiele adresów IP. W takim przypadku Route 53 zwróci wszystkie wartości w losowej kolejności, a [[DNS#DNS Resolver|klient]] losowo wybiera jedną wartość.

Polityka prosta dla rekordu aliasu umożliwia określenie tylko jedenego zasóbu AWS lub jeden rekord w tej samej bieżącej [[#Hosted zone]].

Prosta politykanie **nie** może byż skojażona z [[#Health Check]]'iem.

#### Multivalue answer

**Multiwalue answer routing** pozwala skonfigurować **Route 53** tak, aby w odpowiedzi na zapytania DNS zwracał wiele wartości, takich jak adresy IP serwerów [WWW](http://WWW).

Możesz określić wiele wartości dla niemal każdego rekordu, ale **routing wielowartościowy** pozwala także na sprawdzanie stanu każdego zasobu, dzięki czemu **Route 53** zwraca tylko wartości dla zdrowych zasobów. Route 53 odpowiada na zapytania DNS z maksymalnie ośmioma zdrowymi rekordami i udziela różnych odpowiedzi różnym [[DNS#DNS Resolver|DNS resolvers]].

W przeciwieństwie do [[#Simple|routingu prostego]] można opcjonalnie skojażyć [[#Health Check]]. Jeśli żaden [[#Health Check]] nie zostanie skojażony, **Route 53** uznaje zawsze taki rekord za zdrowy.

#### Latency-based

Aby korzystać z routingu opartego na opóźnieniach, rekordy muszą być utworzone w wielu [[AWS locations#Region|Regionach AWS]].
Kiedy Route 53 otrzymuje zapytanie DNS dla domeny lub subdomeny, sprawdza który region daje użytkownikowi najniższe opóźnienie, a następnie odpowiada wartością z rekordu dla Regionu z najmniejszym opóźnieniem.

#### Weighted

**Routing ważony** pozwala powiązać wiele zasobów z pojedynczą [[DNS#Domain Name|nazwą domeny]] lub subdomeny i wybrać, jaka ilość ruchu jest kierowana do każdego z nich. Nadaje się do balansowania obciążeniem.

**Routing ważony** wymaga, aby każdy [[DNS#DNS Record|rekord]] miał tą samą nazwę i typ dla każdego z zasobów. Każdy taki rekord ma przypisaną względną wagę, w oparciu której Route 53 wysyła ruch do zasobu jako część całkowitej wagi dla wszystkich rekordów w grupie:

![[Route53 policy weighted.png]]

Prosta ważona **może** byż skojażona z [[#Health Check]]'iem.

#### Failover

Dla wielu zasobów, które pełnią tę samą funkcję, można skonfigurować **DNS failover** tak, aby Route 53 kierował ruch z niezdrowego zasobu do zdrowego. Za pomocą [[Route 53 Application Recovery Controller]], można skonfigurować [[#Health Check]]i routingu z **rekordami DNS failover**, aby zarządzać awaryjnym przełączaniem ruchu dla aplikacji.

#### Geolocation

**Routing geolokalizacyjny** pozwala na wybór zasobów obsługujących ruch w oparciu o geograficzną lokalizację użytkowników, czyli lokalizację, z której pochodzą zapytania DNS. Np. wszystkie zapytania z Europy mają być kierowane do load balancera [[Elastic Load Balancing|ELB]] w [[AWS locations#Region|Regionie]] Frankfurtu.

Można określić lokalizacje geograficzne według kontynentu, kraju lub stanu w Stanach Zjednoczonych.

Jeśli utworzysz osobne rekordy dla nakładających się regionów geograficznych - na przykład jeden rekord dla Ameryki Północnej i jeden dla Kanady - priorytet ma najmniejszy region geograficzny. Dzięki temu można kierować część zapytań dotyczących jednego kontynentu do jednego zasobu, a zapytania dotyczące wybranych krajów na tym kontynencie do innego zasobu.

> Kiedy używasz routingu geolokalizacyjnego, możesz zlokalizować swoją zawartość i zaprezentować część lub całość swojej witryny w języku użytkowników. Możesz również użyć routingu geolokalizacyjnego, aby ograniczyć dystrybucję treści tylko do lokalizacji, w których masz prawa do dystrybucji. Innym możliwym zastosowaniem jest równoważenie obciążenia pomiędzy punktami końcowymi w przewidywalny, łatwy do zarządzania sposób, tak aby każda lokalizacja użytkownika była konsekwentnie kierowana do tego samego punktu końcowego.

Można utworzyć **domyślny rekord**, który będzie obsługiwał zarówno zapytania z adresów IP, które nie są mapowane do żadnej lokalizacji, jak i zapytania pochodzące z lokalizacji, dla których nie utworzono rekordów geolokalizacyjnych. Jeśli nie utworzono **rekordu domyślnego**, Route 53 zwróci odpowiedź `no answer` dla zapytań z tych lokalizacji.

#### Geoproximity

Geoproximity routing (tylko z [[#Traffic flow]])
**Routing geoproximity** pozwala kierować ruch do zasobów na podstawie geograficznej lokalizacji użytkowników i zasobów.

Można również opcjonalnie wybrać kierowanie większego lub mniejszego ruchu do danego zasobu poprzez określenie wartości, zwanej stonniczością (**bias**). Wartość **bias** rozszerza lub zmniejsza rozmiar regionu geograficznego, z którego kierowany jest ruch do zasobu. Zakres tej wartości to od **-99** (najmniejszy rozmiar) do **99** (największy rozmiar).

Konfiguracja **routing geoproximity** możliwa jest tylko z użyciem [[#Traffic flow]].

Tworząc reguły **geoproximity** można wybrać ustalić :

- [[AWS locations#Region|Region]] AWS dla zasobów AWS,
- szerokość i długość geograficzna dla zasobu non-AWS.

### Kojarzenie Health Checków z rekordami Route 53

| Typ polityki routingu rekordu Route 53 | Wymagany | Opcjonalny | Niedostępny |
| :------------------------------------- | :------: | :--------: | :---------: |
| [[#Simple]]                            |          |            |      X      |
| [[#Multivalue answer]]                 |          |      X     |             |
| [[#Latency-based]]                     |          |      X     |             |
| [[#Weighted]]                          |          |      X     |             |
| [[#Failover]]                          |  primary |  secondary |             |
| [[#Geolocation]]                       |          |      X     |             |
| [[#Geoproximity]]                      |          |      X     |             |

## Traffic flow

[źródło](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/traffic-flow.html) [źródło](https://geeks.wego.com/multi-region-traffic-engineering/)

Można tworzyć rekordy pojedynczo, ale trudno jest śledzić zależności między nimi, gdy przegląda się ustawienia w konsoli Route 53.

**Traffic flow** znacznie upraszcza proces tworzenia i utrzymywania rekordów w dużych i złożonych konfiguracjach dzięku swoim funkcjonalnościom:

- edytor wizualny, (w tym podgląd mapy),
- wersjonowanie,
- automatyczne tworzenie i aktualizowanie rekordów,
- ronowne wykorzystanie dla wielu rekordów w różnych strefach hostingu;

![[Route 53 Traffic flow.png]]

# Health Check

**Kontrole stanu** Route 53 monitorują stan i wydajność aplikacji internetowych, serwerów WWW i innych zasobów.

Każda utworzona kontrola stanu może monitorować jeden z poniższych elementów:

- health check określonego zasobu (np. serwer WWW),
- health check innych health checków,
- stan [[CloudWatch#Alarm|alarmu CloudWatch]].

> **Health Check** HTTP jest możliwy tylko dla publicznie dostępnych zasobów.

Można również łączyć rezultaty z wielu Health Checków z użyciem:

- **AND**,
- **OR**,
- **NOT**.

---

# See also

- [[DNS]]
