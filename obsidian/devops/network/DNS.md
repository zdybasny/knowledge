#network

[źródło](https://www.cloudflare.com/learning/dns/what-is-dns/)

System nazw domen (**DNS** - **Domain Name System**) jest książką telefoniczną Internetu. Ludzie uzyskują dostęp do informacji online poprzez [Domain Name](#Domain%20Name), takie jak `nytimes.com` lub `espn.com`. Przeglądarki internetowe komunikują się za pomocą adresów IP (Internet Protocol). DNS tłumaczy nazwy domen na adresy IP, dzięki czemu przeglądarki mogą załadować zasoby internetowe.

Każde urządzenie podłączone do Internetu posiada unikalny adres IP, który jest wykorzystywany przez inne maszyny w celu odnalezienia urządzenia. Serwery DNS eliminują konieczność zapamiętywania przez ludzi adresów IP, takich jak `192.168.1.1` (w IPv4), lub bardziej złożonych, nowszych alfanumerycznych adresów IP, takich jak `2400:cb00:2048:1::c629:d7a2` (w IPv6).

##### Spis treści

- [DNS Resolver](#DNS%20Resolver)
  - [DHCP protocol](#DHCP%20protocol)
- [DNS Server](#DNS%20Server)
  - [DNS recursor](#DNS%20recursor)
  - [Nameserver](#Nameserver)
    - [Root nameserver](#Root%20nameserver)
    - [TLD nameserver](#TLD%20nameserver)
    - [Authoritative nameserver](#Authoritative%20nameserver)
- [Domain Name](#Domain%20Name)
  - [Fully Qualified Domain Name](#Fully%20Qualified%20Domain%20Name)
- [Domain Name Registrar](#Domain%20Name%20Registrar)
- [DNS zone](#DNS%20zone)
- [Zone File](#Zone%20File)
  - [DNS Record](#DNS%20Record)
    - [A](#A)
    - [AAAA](#AAAA)
    - [CNAME](#CNAME)
    - [NS](#NS)
    - [SOA](#SOA)
    - [MX](#MX)
    - [SRV](#SRV)
  - [time-to-live](#time-to-live)
- [Domain levels](#Domain%20levels)
  - [Top Level Domain](#Top%20Level%20Domain)
    - [Generic top-level domain](#Generic%20top-level%20domain)
    - [Country code top-level domain](#Country%20code%20top-level%20domain)
  - [Second Level Domain](#Second%20Level%20Domain)
- [DNS protocol](#DNS%20protocol)

# DNS Resolver

DNS resolver to klient DNS w systemie operacyjnym, który zajmuje się rowiązywaniem nazwy (odpytaniem DNS).
Klientami systemu DNS są komputery oraz inne serwery DNS.

Każdy system operacyjny ma wbudowany resolwer, który do działania potrzebuje adresu najbliższego serwera DNS. Adres ten wpisujemy, gdy konfigurujemy adres IP ręcznie lub jest on dostarczany przez serwer [DHCP](#DHCP%20protocol).

## DHCP protocol

[źródło](https://pl.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol)
**Dynamic Host Configuration Protocol** (protokół dynamicznego konfigurowania hostów) jest to protokół komunikacyjny umożliwiający hostom uzyskanie od serwera danych konfiguracyjnych, np. adresu IP hosta, adresu IP bramy sieciowej, adresu [DNS Server](#DNS%20Server), maski podsieci.

# DNS Server

Do ładowania strony internetowej zaangażowane są 4 serwery DNS:

- [DNS recursor](#DNS%20recursor),
- [Root nameserver](#Root%20nameserver),
- [TLD nameserver](#TLD%20nameserver),
- [Authoritative nameserver](#Authoritative%20nameserver).

![DNS servers.png](../attachments/../network/DNS.md%20servers.png)

## DNS recursor

**Rekursor DNS**  jest serwerem zaprojektowanym do odbierania zapytań z [maszyn klienckich](#DNS%20Resolver) poprzez aplikacje takie jak przeglądarki internetowe. Zazwyczaj rekursor jest odpowiedzialny za wykonanie dodatkowych zapytań w celu zaspokojenia zapytania DNS [klienta](#DNS%20Resolver).

> Może być postrzegany jako bibliotekarz, który jest proszony o znalezienie konkretnej książki gdzieś w bibliotece

## Nameserver

**Serwer nazw** jest rodzajem serwera DNS. Jest to serwer, który przechowuje wszystkie rekordy DNS dla domeny, w tym rekordy [A](#A), [MX](#MX) lub [CNAME](#CNAME).

Prawie wszystkie domeny polegają na wielu **serwerach nazw**, aby zwiększyć niezawodność: jeśli jeden **serwer nazw** przestanie działać lub jest niedostępny, zapytania DNS mogą przejść do innego. Zazwyczaj istnieje jeden [Root nameserver](#Root%20nameserver) i kilka drugorzędnych, które przechowują dokładne kopie rekordów DNS. Aktualizacja głównego serwera nazw spowoduje aktualizację również drugorzędnych serwerów nazw.

Gdy używanych jest wiele **serwerów nazw** (jak w większości przypadków), **rekordy NS** powinny wymieniać więcej niż jeden serwer.

### Root nameserver

**Serwer główny** jest pierwszym krokiem w tłumaczeniu (rozwiązywaniu) czytelnych dla człowieka nazw hostów na adresy IP. Zazwyczaj służy jako odniesienie do innych, bardziej specyficznych lokalizacji.

> Można o nim myśleć jak o indeksie w bibliotece, który wskazuje na różne półki z książkami

### TLD nameserver

Server [Top Level Domain TLD](#Top%20Level%20Domain%20TLD)  jest kolejnym krokiem w poszukiwaniu konkretnego adresu IP i przechowuje ostatnią część nazwy hosta (w `example.com`, serwer TLD to `com`).

> Może być postrzegany jako konkretny regał z książkami w bibliotece.

### Authoritative nameserver

**Autorytatywny serwer nazw**  jest ostatnim przystankiem w zapytaniu do serwera nazw. Jeśli autorytatywny serwer nazw ma dostęp do żądanego rekordu, zwróci adres IP dla żądanej nazwy hosta z powrotem do DNS Recursor (bibliotekarz), który złożył początkowe żądanie.

> Może być postrzegany jako słownik na półce z książkami, w którym konkretna nazwa może być przetłumaczona na jej definicję.

# Domain Name

[źródło](https://www.cloudflare.com/learning/dns/glossary/what-is-a-domain-name/)

Nazwa domeny to ciąg tekstu, który odwzorowuje numeryczny adres IP, używany do uzyskania dostępu do strony internetowej z oprogramowania klienckiego. Jest to tekst, który użytkownik wpisuje w oknie przeglądarki, aby dotrzeć do konkretnej strony internetowej. Na przykład, nazwa domeny Google to `google.com`.

Rzeczywisty adres strony internetowej to złożony numeryczny adres IP, ale dzięki DNS użytkownicy mogą wpisywać przyjazne dla człowieka nazwy domen i być kierowani do stron, których szukają. Proces ten znany jest jako DNS lookup.

## Fully Qualified Domain Name

**FQDN** to w pełni kwalifikowana nazwa domeny, która podaje  jej dokładną lokalizację w hierarchii rekordów DNS. J

![DNS FQDN.png](../attachments/../network/DNS.md%20FQDN.png)

# Domain Name Registrar

[źródło](https://en.wikipedia.org/wiki/Domain_name_registrar)

Np. Amazon [Route 53](Route%2053.md), GoDaddy

Rejestrator nazw domen (**Domain Name Registrar**) to firma, która zarządza rezerwacją nazw domen internetowych. Rejestrator musi być akredytowany przez rejestr ogólnych domen najwyższego poziomu (**gTLD** - [Generic top-level domain registry](#Generic%20top-level%20domain%20registry)) lub rejestr krajowych domen najwyższego poziomu (**ccTLD** - [Country code top-level domain](#Country%20code%20top-level%20domain)) Rejestrator działa zgodnie z wytycznymi wskazanych rejestrów nazw domen.

# DNS zone

DNS jest podzielony na wiele różnych **stref**. Strefy te rozróżniają odrębnie zarządzane obszary w przestrzeni nazw DNS. **Strefa DNS** jest częścią przestrzeni nazw DNS, która jest zarządzana przez konkretną organizację lub administratora. **Strefa DNS** jest przestrzenią administracyjną, która pozwala na bardziej granularną kontrolę komponentów DNS, takich jak [Authoritative nameserver](#Authoritative%20nameserver). Przestrzeń nazw domen jest drzewem hierarchicznym, z domeną główną DNS na szczycie. **Strefa DNS** rozpoczyna się od domeny w drzewie i może również rozciągać się w dół do subdomen, tak że wiele
subdomen może być zarządzanych przez jeden podmiot.

![DNS zone.png](../attachments/../network/DNS.md%20zone.png)

Częstym błędem jest utożsamianie **strefy DNS** z nazwą domeny lub pojedynczym [DNS Server](#DNS%20Server). W rzeczywistości, **strefa DNS** może zawierać wiele subdomen i wiele stref może istnieć na tym samym serwerze. **Strefy DNS** nie muszą być fizycznie odseparowane od siebie, strefy są ściśle wykorzystywane do przekazywania kontroli.

# Zone File

**Plik strefy** jest zwykłym plikiem tekstowym przechowywanym w [DNS Server](#DNS%20Server), który zawiera rzeczywistą reprezentację [DNS zone](#DNS%20zone) i zawiera wszystkie [rekordy DNS](#DNS%20Record) dla każdej domeny w strefie. **Pliki strefy** muszą zawsze zaczynać się od rekordu [SOA](#SOA) (Start of Authority), który zawiera ważne informacje, w tym informacje kontaktowe administratora strefy.

## DNS Record

[źródło](https://www.cloudflare.com/learning/dns/dns-records/)

Np. A, AAAA, CNAME, NS

**Rekordy DNS** są instrukcjami, które żyją w autorytatywnych serwerach DNS i dostarczają informacji o domenie, w tym jaki adres IP jest związany z tą domeną i jak obsługiwać żądania dla tej domeny.

Rekordy te składają się z serii plików tekstowych napisanych **składnią DNS**. Składnia DNS to po prostu ciąg znaków używanych jako komendy, które mówią serwerowi DNS, co ma robić. Wszystkie rekordy DNS mają również 'TTL', co oznacza [time-to-live](#time-to-live), i wskazuje jak często serwer DNS będzie odświeżał dany rekord.

Najczęściej stosowane typu rekordów to:

### A

Rekord **A** oznacza "adres" i jest to najbardziej podstawowy typ rekordu DNS: wskazuje on adres IPv4 danej domeny. Rekordy A przechowują tylko adresy IPv4. Jeśli strona ma adres IPv6, zamiast tego użyje rekordu [AAAA](#AAAA).

Np. jeśli wyciągniesz rekordy DNS z cloudflare.com, rekord **A** zwraca obecnie adres IP o wartości: 104.17.210.9.

### AAAA

Rekord **AAAA** dopasowuje nazwę domeny do adresu IPv6. Rekordy **AAAA** są dokładnie takie same jak rekordy [A](#A), z tą różnicą, że przechowują adres IPv6 domeny zamiast jej adresu IPv4.

IPv6 jest najnowszą wersją protokołu internetowego (IP). Jedną z istotnych różnic między IPv6 a IPv4 jest to, że adresy IPv6 są dłuższe niż adresy IPv4. IPv6 oferują wykładniczo więcej permutacji, a tym samym znacznie więcej możliwych adresów IP.

### CNAME

Rekord **CNAME** (**canonical name** - nazwa kanoniczna) jest używany zamiast rekordu [A](#A), gdy domena lub subdomena jest aliasem innej domeny. Wszystkie rekordy **CNAME** muszą wskazywać na domenę, nigdy na adres IP. Domena z rekordem CNAME może wskazywać na inną domenę z rekordem **CNAME** lub na domenę z rekordem [A](#A).

Często, gdy strony mają subdomeny takie jak blog.example.com lub shop.example.com, te subdomeny będą miały rekordy **CNAME**, które wskazują na domenę główną (example.com). W ten sposób, jeśli adres IP hosta się zmieni, tylko rekord DNS [A](#A) dla domeny głównej musi zostać zaktualizowany, a wszystkie rekordy CNAME będą podążać za zmianami dokonanymi w domenie głównej.

### NS

Rekord **NS** (**Name Server** - server nazw) wskazuje, który serwer DNS jest autorytatywny dla tej domeny (tj. który serwer zawiera rzeczywiste rekordy DNS). Zasadniczo, rekordy **NS** mówią Internetowi, gdzie ma się udać, aby znaleźć adres IP domeny. Domena często posiada wiele rekordów **NS**, które mogą wskazywać na pierwotnych i wtórnych serwerów nazw dla tej domeny. Bez prawidłowo skonfigurowanych rekordów NS, użytkownicy nie będą w stanie załadować strony internetowej lub aplikacji.

### SOA

Rekord **SOA** (**start of authority**) przechowuje informacje o domenie lub strefie, m.in.:

- adres e-mail administratora,
- kiedy domena była ostatnio aktualizowana,
- jak długo serwer powinien czekać pomiędzy kolejnymi odświeżeniami.

Wszystkie strefy DNS potrzebują rekordu **SOA** w celu zachowania zgodności ze standardami **IETF**. Rekordy **SOA** są również ważne dla transferów stref.

### MX

Rekord MX (**mail exchange** - wymiany poczty) kieruje pocztę elektroniczną do serwera pocztowego. **MX** wskazuje, w jaki sposób wiadomości e-mail powinny być kierowane zgodnie z protokołem **SMTP** (Simple Mail Transfer Protocol). Podobnie jak [CNAME](#CNAME), **MX** musi zawsze wskazywać na inną domenę.

### SRV

Rekord **SRV** (**service)**) określa **hosta** i **port** dla konkretnych usług, takich jak VoIP (voice over IP), komunikatory internetowe itp. Większość innych rekordów DNS określa tylko serwer lub adres IP, ale rekordy **SRV** zawierają również port pod tym adresem IP. Niektóre protokoły internetowe wymagają użycia rekordów **SRV**, aby móc funkcjonować.

## time-to-live

[źródło](https://support.google.com/a/answer/48090)

Wartość **TTL** to wartość w [DNS Record](#DNS%20Record), która określa liczbę sekund między wprowadzaniem kolejnych zmian w rekordach. Każdy z rekordów w domenie ma wartość **TTL**. Bieżąca wartość TTL rekordu określa, jak długo trzeba czekać na zastosowanie wprowadzonych zmian.

> Google zaleca ustawienie wartości TTL na 3600, co oznacza, że serwery internetowe będą sprawdzać aktualność rekordu co godzinę.

# Domain levels

## Top Level Domain

Np. gTLD: .com, .gov, .org / ccTLD: .us, .pl, .de

### Generic top-level domain

[źródło](https://en.wikipedia.org/wiki/Generic_top-level_domain)

Np. .com, .gov, .org

Ogólne domeny najwyższego poziomu (**gTLD**) są jedną z kategorii domen najwyższego poziomu (TLD) utrzymywanych przez Internet Assigned Numbers Authority (IANA) do użytku w systemie nazw domen w Internecie. Domena najwyższego poziomu jest ostatnim poziomem każdej w pełni kwalifikowanej nazwy domeny.

Są one nazywane ogólnymi z powodów historycznych; początkowo były one przeciwstawiane [krajowym TLD](#Country%20code%20top-level%20domain) w RFC 920.

### Country code top-level domain

[źródło](https://en.wikipedia.org/wiki/Domain_name_registrar)

Np. .us, .pl, .de

Domena najwyższego poziomu z kodem kraju (**ccTLD**) jest internetową domeną najwyższego poziomu zazwyczaj używaną lub zarezerwowaną dla kraju, suwerennego państwa lub terytorium zależnego, identyfikowanego za pomocą kodu kraju. Wszystkie identyfikatory ASCII **ccTLD** mają długość dwóch liter, a wszystkie dwuliterowe domeny najwyższego poziomu są **ccTLD**.

## Second Level Domain

[źródło](https://www.domain.com/blog/what-is-a-second-level-domain/)

Np. **amazon**.com, **google**.com

Domena drugiego poziomu (**SLD**) to nazwa znajdująca się na lewo od rozszerzenia domeny [Top Level Domain](#Top%20Level%20Domain).
**SLD** jest pierwszym punktem kontaktu internautów z witryną. To najbardziej zapamiętywalna część adresu URL i dlatego jest najważniejsza.

# DNS protocol

[źródło](https://pl.wikipedia.org/wiki/Domain_Name_System)

**DNS** to również protokół komunikacyjny opisujący sposób łączenia się [klientów](#DNS%20Resolver) z [serwerami DNS](#DNS%20Server). Częścią specyfikacji protokołu jest również zestaw zaleceń, jak aktualizować wpisy w bazach domen internetowych.

Standardowy port DNS to 53.

Na świecie jest wiele serwerów DNS, które odpowiadają za obsługę poszczególnych domen internetowych. Domeny mają strukturę drzewiastą, na szczycie znajduje się 13 głównych serwerów (_root servers_) obsługujących domeny najwyższego poziomu (TLD – top level domains).
