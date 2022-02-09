#AWS #storage

EBS jest to usługa, która dostarcza [voluminy](#EBS%20Volume) pamięci blokowej (takiej jak SSD, HDD) do [EC2](EC2.md).

Może skalować się do obsługi petabajtów danych, a także obsługiwać wiele typów woluminów w zależności od potrzeb. Może się przydać w przypadku okresowego tworzenia kopi zapasowych danych z dysku podłączonym do instancji [EC2](EC2.md). Oferuje również szyfrowanie woluminów.

##### Spis treści

- [Volume](#Volume)
  - [Typy EBS Volume](#Typy%20EBS%20Volume)
    - [Solid state drive (SSD)](#Solid%20state%20drive%20(SSD))
    - [General purpose SSB - gp](#General%20purpose%20SSB%20-%20gp)
    - [Provisioned IOPS SSD - io](#Provisioned%20IOPS%20SSD%20-%20io)
    - [Hard disk drive (HDD)](#Hard%20disk%20drive%20(HDD))
    - [Throughput Optimizad HDD - st](#Throughput%20Optimizad%20HDD%20-%20st)
    - [Cold HDD - sc](#Cold%20HDD%20-%20sc)
- [Multi-Attach](#Multi-Attach)
- [Szyfrowanie](#Szyfrowanie)
- [Snapshot](#Snapshot)
- [See also](#See%20also)

# Volume

**EBS Volume** to sterownik sieciowy, który łączy [EC2](EC2.md) z pamięcią masową, co oznacza możliwość wystąpienia niewielkich opóźnień w transferze. Cykl życia EBS Volume jest niezależny od cyklu życia [EC2](EC2.md), dlatego może być dołączany i odłączany od instacji. Voluminy zachowują się jak surowe niesformatowane urządzenia blokowe.

Voluminy działają w ramach pojedynczej [AZ](AWS%20locations.md#Availability%20zone).

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

Amazon EBS Multi-Attach umożliwia dołączenie pojedynczego wolumenu **Provisioned IOPS SSD (io1 lub io2)** do wielu instancji [EC2](EC2.md), które znajdują się w tej samej [AZ](AWS%20locations.md#Availability%20zone). Do [EC2](EC2.md) lub zestawu instancji można dołączyć wiele woluminów z włączoną funkcją Multi-Attach. Każda [EC2](EC2.md), do której wolumen jest dołączony, ma pełne uprawnienia do odczytu i zapisu do udostępnionego wolumenu.

Multi-Attach ułatwia osiągnięcie wyższej dostępności aplikacji w klastrowych aplikacjach systemu Linux, które zarządzają współbieżnymi operacjami zapisu.

> Aplikacje kożystające z voluminów Multi-Attach same musą zadbać o poprawną obsługę współbierznego zapisu/odczytu.

# Szyfrowanie

#security

Szyfrowany volumin EBS dotyczy danych **podczas transferu** oraz **w spoczynku**. Jest to proces niazuważalny dla użytkownika, ponieważ jest zarządny przez AWS z kluczami (**AES-256**) trzymanymi w [Key Management Service](Key%20Management%20Service.md) .

Włączyć szyfrowanie EBS można na 2 sposoby:

- podczas tworzenia EBS Volume (również z nieszyfrowanych snapshotów)
- podczas kopiowania snapshotów (volumen z takiego snapshptu jest zawsze szysforwany)

# Snapshot

Snapshoty służą głównie do tworzenia back-up'ów, poprzez robienie zrzutu stanu voluminu EBS oraz tworzenia voluminów z takich snapshotów. Można je przenosić także pomiędzy [AZ](AWS%20locations.md#Availability%20zone) oraz [regionami](AWS%20locations.md#Region).

> Zaleca się odpiąć EBS Volume na czas wykonywania snapshotu, choć nie jest to wymagane.

---
# See also

- [EC2 Instance Store](EC2%20Instance%20Store.md)
- [Elastic File System](Elastic%20File%20System.md)
- [RDS](RDS.md)