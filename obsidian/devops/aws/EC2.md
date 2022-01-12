#AWS #computing

Amazon Elastic Compute Cloud (Amazon EC2) oferuje platformę obliczeniową, z instancjami i wyborem  procesora, pamięci masowej, sieci, systemu operacyjnego i modelu zakupu.

# Typy instancji

Typy instancji Amazon EC2 są zoptymalizowane do różnych zadań. Wybierając typ instancji, weź pod uwagę specyficzne potrzeby Twoich obciążeń i aplikacji.
Może to obejmować wymagania dotyczące:

- mocy obliczeniowej,
- pamięci,
- możliwości przechowywania danych.

**Typy instancji:**

## Ogólnego zastosowania

Ddostarcza zbalansowane zasoby (CPU, RAM, dysk, sieciowe) przeznaczona dla aplikacji, które nie potrzebują optymalizacji pod kątem żadnego konretnego zasobu
Zastosowanie:

- serwery aplikacji (w tym backend),
- serwery do gier,
- małe i średnie bazy danych.

## Zoptymalizowana pod kątem obliczeń (compute)

Dostarczają wysoko wydajne procesory.
Zastosowanie:

- aplikacje i serwery, które wymagają wiele obliczeń lub przetwarzania wielu transakcji.

## Zoptymalizowana pod kątem pamięci (momory)

Zapewnia szybką wydajność dla obciążeń, które przetwarzają duże zbiory danych w pamięci.
Zastosowanie:

- obciążenia wygmagające załadowania dużej ilości danych przed uruchomieniem lub w trakcie działania plikacji,
- bazy danych przetwarzające duże ilości danych w czasie rzeczywistym.

## Zoptymalizowana pod kątem pamięci masowej (storage)

Zastosowanie:

- obciążenia wymagające wysokiego, sekwencyjnego dostępu i/o dużych zbiorów danych (_IOPS_ - input output per second)
- duże bazy danych,
- rozproszone systemy plików,
- hurtownie danych,
- systemy przetwarzania transakcji online (_OLTP_) o wysokiej częstotliwości.

## Ze wsparciem sprzętowym

Wykorzystują akceleratory sprzętowe lub koprocesory do wykonywania niektórych funkcji w sposób bardziej efektywny niż jest to możliwe w oprogramowaniu działającym na procesorach. Przykłady takich funkcji obejmują obliczenia na liczbach zmiennoprzecinkowych, przetwarzanie grafiki i dopasowywanie wzorców danych.
Zastosowanie:

- aplikacje graficzne,
- strumieniowe przesyłanie gier
- strumieniowe przesyłanie aplikacji

# Opcje kupna

## on-demand

**(Domyśłna)**
Instancje na żądanie pozwalają Ci płacić za moc obliczeniową za godzinę lub sekundę (minimum 60 sekund) bez żadnych długoterminowych zobowiązań. Uwalnia Cię to od kosztów i zawiłości związanych z planowaniem, zakupem i utrzymaniem sprzętu, a także przekształca zwykle duże koszty stałe w znacznie mniejsze koszty zmienne.

## Reserved Instances

Zapewnia zniżki w stosunku do modelu On-Demand, gdy możesz zobowiązać się do określonego okresu (1 lub 3 lata). Dodatkowo, gwarantuje on również rezerwację pojemności dla określonego typu instancji. na cały wykupiony okres.

Dostępne są 3 plany rezerwacji instancji:

- Standard
- Convertible
- Scheduled

### Standard

Daje najwyższy rabat i to działa dobrze tylko dla stałych obciążeń roboczych. Oznacza to, że nie można zmienić instancji w ciągu zamówionego okresu.

### Convertible

Pozwala na konwersję niektórych atrybutów, jeśli będzie to miało taką samą wartość. Jest to również świetne rozwiązanie dla stabilnych obciążeń.

### Scheduled

Scheduled Reserved Instance opłaca się w przypadku obciążeń zmiennych, ale przewidywalnych, np. wzmożony ruch w okresie świątecznym.

## Savings Plan

Podobny do instancji zarezerwowanych, z tą różnicą, że nie ogranicza się tylko do EC2. Obsługuje również [[Fargate]] i [[AWS Lambda]]. W przeciwieństwie do Reserved Instances nie rezerwuje pojemności.

## Spot

Pozwala na wykorzystanie nadmiaru mocy obliczeniowej EC2, które mogą istnieć w [[AWS locations#Availability zone|Availability zone]]. Pozwala uzyskać nawet 90% zniżki. Istnieje cena rynkowa dla typów instancji na strefę dostępności i jest to tzw. **cena spot** (spot prise). Jeśli twoja oferta jest wyższa niż cena spot, będziesz mógł uruchomić swoje instancje. Jeśli cena spot przekroczy twoją ofertę, wtedy twoja instancja zostanie zamknięta w ciągu 2 minut. Z tego powodu nadaje się w przypadku obciążeń, które mogą się uruchamiać i zamykać bez negatywnego wpływu na system.

## Dedicated Host

Dostarcza dedykowany serwer fizyczny w centrum danych (data centre). Jest to najdroższa opcja. Przykładowym przypadkiem jego zapotrzebowania jest pewność przestrzegania warunków licencji per-serwer, per-core, per-VM, per-cocket.
