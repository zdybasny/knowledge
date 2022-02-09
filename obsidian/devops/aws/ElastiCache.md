#AWS #database

[źródło](https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/WhatIs.html)

**ElastiCash** to pełni zarządzana usługa pamięci podręcznej, czyli bazy danych trzymanej w pamięci w celu zapewnienia bardzo wysokich wydajności bardzo niskich opóźnień. Gwarantuje niski czas odpowiedzi, skalowanie i replikację, aby sprostać żądaniom aplikacji. Odciąża bazy danych (np. [RDS](RDS.md)) przy intensywnym odczycie.

ElastiCashe wspiera **Memcached** oraz **Redis** oraz bsługuje powszechne przypadki użycia, w tym cache dla baz danych i pamięć sesji. Obie technologie są przechowują dane klucz-wartość (**noSQL**).

Użycie ElastiCache wymaga dużych zmian w kodzie aplikacji. Cache musi posiada strategię inwalidacji, aby
upewnić się, że tylko najbardziej aktualne dane są tam przechowywane. Jeśli żądanych danych nie ma w cashe, wykonywane jest zapytanie do zwykłej bazy danych.

##### Spis treści

- [Porównanie Memcashed i Redis](#Porównanie%20Memcashed%20i%20Redis)
- [See also](#See%20also)

# Porównanie Memcashed i Redis
[źródło AWS](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/SelectEngine.html) [źródło](https://www.imaginarycloud.com/blog/redis-vs-memcached/) [źródło](https://faun.pub/aws-solutions-architect-associate-cheat-sheet-6991d2f6cb7d)

**Memcached** nadaje się jeśli potrzeba:

- jak najprostszego modelu:
  - klucze string-string;
- uruchomić duże węzły z wieloma rdzeniami lub wątkami;
- możliwości skalowania out/in, (dodaje i usuwa węzły w miarę zmiany zapotrzebowania);
- potrzeba cache'ować obiekty;
- partycjonowania danych (**sharding**) między węzłami.

**Redis** nadaje się jeśli potrzeba:

- złożonego modelu:
  - string, hash, list, set, sorted set,
  - strumienie Redis,
  - struktury logów, która pozwala na dodawanie nowych elementów w czasie rzeczywistym, a także pozwala konsumentom na konsumpcję wiadomości w sposób blokujący lub nieblokujący;
- multi-AZ w automatycznym przepinaniem awaryjnym (**auto-failover**);
- automatycznego backupu oraz przywracania;
- wysokiej dostępności podczas odczytu (read replicas);
- szyfrowania danych;
- perzystencji danych:
  - **RDB snapshot** - zrzut całego zbioru danych wykonywany w określonych odstępach czasu, przechowywany w pliku,
  - **AOF log** -(Append Only File) zbiór wszystkich poleceń zapisu wykonanych na serwerze, przechowywany jako plik.

---

# See also

- [RDS](RDS.md)
