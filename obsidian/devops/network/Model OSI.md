#network

[źródło](https://pl.wikipedia.org/wiki/Model_OSI)

Model OSI (ang. **Open Systems Interconnection** Model) składaj się z siedmiu warstw:

1. fizycznej,
2. łącza danych,
3. sieci,
4. transportu,
5. sesji,
6. prezentacji,
7. aplikacji.

Każdy protokół sieciowy przypisany jest do jednej z warstw. Poszczególne warstwy realizują w związku z tym różne usługi. Z usług dostarczanych przez warstwę korzysta warstwa znajdująca się bezpośrednio nad nią. Jest to tak zwany warstwowy model usług.

![](attachments/Model%20OSI.png)

> Warstwowy model sieci oferuje nie tylko pojęciowe i strukturalne uporządkowanie, ale także umożliwia przezroczyste dla wyższych warstw zmiany sposobu realizacji usług znajdujących się poniżej. Dobrym przykładem korzyści płynących ze ścisłego powiązania warstw z realizowanymi przez nie funkcjami jest dynamiczny rozwój sieci bezprzewodowych w standardzie 802.11. Dla przeglądarki internetowej używającej na poziomie warstwy aplikacji protokołu HTTP, czy dla klienta FTP nie ma znaczenia czy na warstwie łącza danych używany jest protokół Ethernet 802.11, czy też jakiś inny (pod warunkiem, że realizuje funkcje przypisane do tej warstwy).

# Warstwy OSI

## Layer 1

**Wartwa fizyczna**

Wykorzystuje fizyczne medium (powietrze, przewody miedziane, światłowody) i jego własności do przesyłania sygnałów. Elementami tej warstwy są karty sieciowe, okablowanie. 

Czasami nazywa się ją warstwą nieinteligentną ponieważ nie kontroluje przeznaczenia sygnałów podczas ich przesyłania,

## Layer 2

**Wartswa łącza danych**

**Pzykładowe protokoły**: Ethernet 802.11, token ring, PPP.

Wykonuje funkcję nadzoru jakości przekazywanych informacji w stosunku do warstwy znajdującej się pod nią. Kontroluje ilość przesyłanych danych w zależności od chwilowych parametrów warstwy fizycznej.

## Layer 3

**Wartswa sieci**

**Pzykładowe protokoły**: IP, IPX.

Główną funkcją tej warstwy jest umożliwienie komunikacji pomiędzy komputerami znajdującymi się w różnych miejscach (różnych segmentach sieci). W związku z tym warstwa ta udostępnia usługę identyfikacji ścieżki jaka prowadzi od hosta źródłowego do docelowego.

Najbardziej rozpowszechniony w Internecie protokół tej warstwy to **IP**; nie gwarantuje on, że wysłane pakiety dotrą do adresata lub, że będą w właściwej kolejności.

## Layer 4

**Wartswa transportu**

**Pzykładowe protokoły**: TCP, UDP, SSL/TLS.

Zapewnia niezawodną komunikację pomiędzy komputerami znajdującymi się w dowolnych punktach sieci, korzystając z usług dostarczanych przez warstwy znajdujące się poniżej.

Warstwa transportu wykrywa zagubione pakiety i żąda retransmisji, a także potrafi zidentyfikować ich poprawną kolejność. Dodatkowo warstwa ta umożliwia podział kanału łączności pomiędzy wiele połączeń sieciowych. 

Na poziomie warstwy sieciowej wprowadzone jest także pojęcie gniazda (ang. **socket**), które to umożliwia niezależne działanie wielu usług sieciowych.

## Layer 5

**Wartswa sesji**

## Layer 6

**Wartswa prezentacji**

## Layer 7

**Wartswa aplikacji**

**Pzykładowe protokoły**: HTTP, [DNS](DNS.md), POP3, FTP, SSH.

 Zadania realizowane przez tą warstwę są najbardziej różnorodne i jednocześnie najbliższe potrzebom użytkownika. Na warstwie aplikacji realizowane są funkcje, które są niezbędne do świadczenia usług końcowych, takich jak dyski sieciowe, udostępnianie drukarek czy treści. 
 
 Najpopularniejszym przykładem protokołu warstwy aplikacji jest HTTP.

 # Kapsułkowanie

 Kapsułkowanie (ang. **encapsulation**) jest procesem funkcjonowania sieci komputerowych. W skrócie polega na dodawania nagłówków do ramki komunikatu na każdej warstwie hosta nadawcy, 
 
 Natomiast w hoście docelowym następuje proces odwrotny (ang. **decapsulation**). Najpierw z bitów składana jest ramka. Później po odjęciu nagłówka warstwy łącza danych powstaje datagram. Kolejnymi krokami jest odcięcie nagłówków warstwy sieci i transportowej i przekazanie do warstwy aplikacji samego komunikatu.

 ![](attachments/OSI%20encapsulation.png)