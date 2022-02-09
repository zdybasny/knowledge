#AWS #network

# Internet Gateway

Internet Gateway to skalowany horyzontalnie i wysoce dostępny komponent VPC, który umożliwia komunikację między [VPC](VPC.md) a Internetem.

Internet Gateway służy do dwóch celów:

- zapewnienia docelowego miejsca w [VPC Route table](VPC%20Route%20table.md) dla ruchu trasowalnego przez Internet,
- wykonywania translacji adresów sieciowych ([NAT](../network/NAT.md)) dla instancji, którym przypisano publiczne adresy IPv4.

Internet Gateway obsługuje ruch IPv4 i IPv6. Nie ogranicza przepustowości ruchu sieciowego.

Posiadanie bramy internetowej na koncie nie wiąże się z żadnymi dodatkowymi opłatami.

[EC2](EC2.md) jest świadoma tylko prywatnej (wewnętrznej) przestrzeni adresowej IP zdefiniowanej w ramach [VPC](VPC.md) i [VPC Subnet](VPC%20Subnet.md)s. Internet Gateway logicznie zapewnia [NAT](../network/NAT.md) jeden-do-jednego dla instancji, więc kiedy ruch opuszcza [VPC Subnet](VPC%20Subnet.md) i kieruje się do Internetu, pole adresu odpowiedzi jest ustawione na publiczny adres IPv4 lub adres [Elastic IP](Elastic%20IP.md) instancji. I odwrotnie, ruch kierowany na publiczny adres IPv4 lub [Elastic IP](Elastic%20IP.md) ma tłumaczony adres docelowy na prywatny adres IPv4 instancji, zanim zostanie dostarczony do [VPC](VPC.md).

---

# See also:

- [NAT Gateway](NAT%20devices.md#NAT%20Gateway)
