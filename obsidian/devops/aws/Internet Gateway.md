#AWS #network

# Internet Gateway

Internet Gateway to skalowany horyzontalnie i wysoce dostępny komponent VPC, który umożliwia komunikację między [[VPC]] a Internetem.

Internet Gateway służy do dwóch celów:

- zapewnienia docelowego miejsca w [[VPC Route table]] dla ruchu trasowalnego przez Internet,
- wykonywania translacji adresów sieciowych ([[NAT]]) dla instancji, którym przypisano publiczne adresy IPv4.

Internet Gateway obsługuje ruch IPv4 i IPv6. Nie ogranicza przepustowości ruchu sieciowego.

Posiadanie bramy internetowej na koncie nie wiąże się z żadnymi dodatkowymi opłatami.

[[EC2]] jest świadoma tylko prywatnej (wewnętrznej) przestrzeni adresowej IP zdefiniowanej w ramach [[VPC]] i [[VPC Subnet]]s. Internet Gateway logicznie zapewnia [[NAT]] jeden-do-jednego dla instancji, więc kiedy ruch opuszcza [[VPC Subnet]] i kieruje się do Internetu, pole adresu odpowiedzi jest ustawione na publiczny adres IPv4 lub adres [[Elastic IP]] instancji. I odwrotnie, ruch kierowany na publiczny adres IPv4 lub [[Elastic IP]] ma tłumaczony adres docelowy na prywatny adres IPv4 instancji, zanim zostanie dostarczony do [[VPC]].

---

# See also:

- [[NAT devices#NAT Gateway]]
