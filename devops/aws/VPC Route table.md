#AWS #network

# Trasowanie VPC

Podczas tworzenia [[VPC]] należy określić zakres adresów IPv4 dla [[VPC]] w postaci bloku **Classless Inter-Domain Routing** ([[CIDR]]), na przykład 10.0.0.0/16. Poniższy diagram przedstawia nowe VPC z blokiem CIDR IPv4.

![[VPC CIDR.png]]

W głównej tabeli trasowanie znajdują się następujące trasy:

| Destination | Target |
| ----------- | ------ |
| 10.0.0.0/16 | local  |

# Trasowanie Podsieci

Podczas tworzenia podsieci określa się blok [[CIDR]] dla podsieci, który jest podzbiorem bloku [[#Trasowanie VPC|CIDR VPC]].

![[VPC Tracing.png]]

Każda [[VPC Subnet]] musi być skojarzona z tablicą trasowania, która określa dozwolone trasy dla ruchu wychodzącego z danej [[VPC Subnet]]. Każda utworzona [[VPC Subnet]] jest automatycznie kojarzona z główną tabelą dla [[VPC]]. Można zmienić to skojarzenie oraz zawartość głównej tabeli trasowania.

Instancja 2A nie może dotrzeć do Internetu, ale może dotrzeć do innych instancji w [[VPC]]. Można pozwolić instancji w [[VPC]] na inicjowanie połączeń wychodzących do Internetu przez IPv4, ale uniemożliwić niechciane połączenia przychodzące z Internetu za pomocą [[NAT devices#NAT Gateway|NAT Gateway]] lub [[NAT devices#NAT Inctance|NAT Instance]].

Ponieważ można przydzielić ograniczoną liczbę adresów [[Elastic IP]], zaleca się korzystanie z [[NAT devices]] w przypadku większej liczby [[EC2]] wymagających statycznego publicznego adresu IP.

## Public subnet

Jeśli podsieć jest skojarzona z [[VPC Route table]], która ma trasę do [[Internet Gateway]], jest to tzw. **podsieć publiczna**.

## Private subnet

Jeśli podsieć jest skojarzona z [[VPC Route table]], która **NIE** zawiera trasy do [[Internet Gateway]], jest to podsieć prywatna. Taka podsieć nadal może trasować do [[NAT devices#NAT Gateway|NAT Gateway]].
