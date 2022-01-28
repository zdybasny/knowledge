#AWS #compute #storage 

[źródło](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html#instance-store-volumes)

**Instancji Store** zapewnia tymczasową pamięć masową na poziomie bloków dla [[EC2]]. Pamięć ta znajduje się na dyskach, które są fizycznie podłączone do komputera hosta. Dzięki temu zapewnia najwyższą tydajność I/O, ale dane są tracone po zatrzymaniu [[EC2]]. 

Instance Store jest idealny do tymczasowego przechowywania informacji, które często się zmieniają, takich jak bufory, pamięci podręczne, dane typu scratch i inne tymczasowe treści, lub dla danych, które są replikowane między flotą instancji, takich jak zrównoważona pod względem obciążenia pula serwerów internetowych.

Instance Store składa się z jednego lub więcej woluminów, wystawionych jako urządzenia blokowe. Rozmiar Instance Store, jak również liczba dostępnych urządzeń różni się w zależności od typu instancji.

![[EC2 Instance Store.png]]

---
# See also

- [[Elastic Block Store]]
- [[Elastic File System]]
