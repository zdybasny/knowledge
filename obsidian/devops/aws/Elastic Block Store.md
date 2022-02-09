#AWS #storage

EBS jest to usługa, która dostarcza [[#EBS Volume|voluminy]] pamięci blokowej (takiej jak SSD, HDD) do [[EC2]].

Może skalować się do obsługi petabajtów danych, a także obsługiwać wiele typów woluminów w zależności od potrzeb. Może się przydać w przypadku okresowego tworzenia kopi zapasowych danych z dysku podłączonym do instancji [[EC2]]. Oferuje również szyfrowanie woluminów.

##### Spis treści

- [[#Volume]]
  - [[#Typy EBS Volume]]
    - [[#Solid state drive (SSD)]]
    - [[#General purpose SSB - gp]]
    - [[#Provisioned IOPS SSD - io]]
    - [[#Hard disk drive (HDD)]]
    - [[#Throughput Optimizad HDD - st]]
    - [[#Cold HDD - sc]]
- [[#Multi-Attach]]
- [[#Szyfrowanie]]
- [[#Snapshot]]
- [[#See also]]

# Volume

**EBS Volume** to sterownik sieciowy, który łączy [[EC2]] z pamięcią masową, co oznacza możliwość wystąpienia niewielkich opóźnień w transferze. Cykl życia EBS Volume jest niezależny od cyklu życia [[EC2]], dlatego może być dołączany i odłączany od instacji. Voluminy zachowują się jak surowe niesformatowane urządzenia blokowe.

Voluminy działają w ramach pojedynczej [[AWS locations#Availability zone|AZ]].

Opłaty naliczane są z góry za:

- pojemność,
- liczbę operacji na sekundę (IOPS.)

## Typy EBS Volume

Dotępne następujące tyly volimunów.

### Solid state drive (SSD)

Zoptymalizowany pod kątem obciążeń transakcyjnych obejmujących częste operacje odczytu/zapisu przy niewielkim rozmiarze operacji I/O, gdzie dominującym atrybutem wydajności jest IOPS.

### General purpose SSB - gp

Zapewnia równowagę między ceną a wydajnością. Zalecane do większości obciążeń roboczych.

### Provisioned IOPS SSD - io

Zapewnia wysoką wydajność w przypadku obciążeń o znaczeniu krytycznym, niskich opóźnieniach lub wysokiej przepustowości.

### Hard disk drive (HDD)

Zoptymalizowany dla dużych obciążeń strumieniowych, gdzie dominującym atrybutem wydajności jest przepustowość.

### Throughput Optimizad HDD - st

Tani dysk twardy przeznaczony do często używanych, wymagających dużej przepustowości zadań.

### Cold HDD - sc

Najtańszy dysk twardy przeznaczony do zastosowań wymagających rzadszego dostępu do danych.

# Multi-Attach

[źródło](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volumes-multi.html)

Amazon EBS Multi-Attach umożliwia dołączenie pojedynczego wolumenu **Provisioned IOPS SSD (io1 lub io2)** do wielu instancji [[EC2]], które znajdują się w tej samej [[AWS locations#Availability zone|AZ]]. Do [[EC2]] lub zestawu instancji można dołączyć wiele woluminów z włączoną funkcją Multi-Attach. Każda [[EC2]], do której wolumen jest dołączony, ma pełne uprawnienia do odczytu i zapisu do udostępnionego wolumenu.

Multi-Attach ułatwia osiągnięcie wyższej dostępności aplikacji w klastrowych aplikacjach systemu Linux, które zarządzają współbieżnymi operacjami zapisu.

> Aplikacje kożystające z voluminów Multi-Attach same musą zadbać o poprawną obsługę współbierznego zapisu/odczytu.

# Szyfrowanie

#security

Szyfrowany volumin EBS dotyczy danych **podczas transferu** oraz **w spoczynku**. Jest to proces niazuważalny dla użytkownika, ponieważ jest zarządny przez AWS z kluczami (**AES-256**) trzymanymi w [[Key Management Service]] .

Włączyć szyfrowanie EBS można na 2 sposoby:

- podczas tworzenia EBS Volume (również z nieszyfrowanych snapshotów)
- podczas kopiowania snapshotów (volumen z takiego snapshptu jest zawsze szysforwany)

# Snapshot

Snapshoty służą głównie do tworzenia back-up'ów, poprzez robienie zrzutu stanu voluminu EBS oraz tworzenia voluminów z takich snapshotów. Można je przenosić także pomiędzy [[AWS locations#Availability zone|AZ]] oraz [[AWS locations#Region|regionami]].

> Zaleca się odpiąć EBS Volume na czas wykonywania snapshotu, choć nie jest to wymagane.

---
# See also

- [[EC2 Instance Store]]
- [[Elastic File System]]
- [[RDS]]