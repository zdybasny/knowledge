#AWS #network

# Trasowanie VPC

Podczas tworzenia [VPC](VPC.md) należy określić zakres adresów IPv4 dla [VPC](VPC.md) w postaci bloku **Classless Inter-Domain Routing** [CIDR](../network/CIDR.md)), na przykład 10.0.0.0/16. Poniższy diagram przedstawia nowe VPC z blokiem CIDR IPv4.

![VPC CIDR.png](VPC%20CIDR.png.md)

W głównej tabeli trasowanie znajdują się następujące trasy:

| Destination | Target |
| ----------- | ------ |
| 10.0.0.0/16 | local  |

# Trasowanie Podsieci

Podczas tworzenia podsieci określa się blok [CIDR](../network/CIDR.md) dla podsieci, który jest podzbiorem bloku [CIDR VPC](#Trasowanie%20VPC).

![VPC Tracing.png](VPC%20Tracing.png.md)

Każda [VPC Subnet](VPC%20Subnet.md) musi być skojarzona z tablicą trasowania, która określa dozwolone trasy dla ruchu wychodzącego z danej [VPC Subnet](VPC%20Subnet.md). Każda utworzona [VPC Subnet](VPC%20Subnet.md) jest automatycznie kojarzona z główną tabelą dla [VPC](VPC.md). Można zmienić to skojarzenie oraz zawartość głównej tabeli trasowania.

Instancja 2A nie może dotrzeć do Internetu, ale może dotrzeć do innych instancji w [VPC](VPC.md). Można pozwolić instancji w [VPC](VPC.md) na inicjowanie połączeń wychodzących do Internetu przez IPv4, ale uniemożliwić niechciane połączenia przychodzące z Internetu za pomocą [NAT Gateway](NAT%20devices.md#NAT%20Gateway) lub [NAT Instance](NAT%20devices.md#NAT%20Inctance).

Ponieważ można przydzielić ograniczoną liczbę adresów [Elastic IP](Elastic%20IP.md), zaleca się korzystanie z [NAT devices](NAT%20devices.md) w przypadku większej liczby [EC2](EC2.md) wymagających statycznego publicznego adresu IP.

## Public subnet

Jeśli podsieć jest skojarzona z [VPC Route table](VPC%20Route%20table.md), która ma trasę do [Internet Gateway](Internet%20Gateway.md), jest to tzw. **podsieć publiczna**.

## Private subnet

Jeśli podsieć jest skojarzona z [VPC Route table](VPC%20Route%20table.md), która **NIE** zawiera trasy do [Internet Gateway](Internet%20Gateway.md), jest to podsieć prywatna. Taka podsieć nadal może trasować do [NAT Gateway](NAT%20devices.md#NAT%20Gateway).
