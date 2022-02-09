#AWS #network

# Amazon VPC

Amazon VPC to wirtualna chmura prywatna, która jest logicznie odizolowana od publicznej części. Można na w niej uruchamiać usługi i zasoby AWS.

Umożliwia to posiadanie wirtualnej sieci (VPN), którą definiujesz i możesz konfigurować. VPC obsługuje zarówno IPv4 jak i IPv6.

Można skonfigurować:

- zakres adresów IP [CIDR](../Network/CIDR.md) [CIDR](../Network/CIDR.md)),
- podsieci ([VPC Subnet](VPC%20Subnet.md)),
- tabele tras ([VPC Route table](VPC%20Route%20table.md)),
- bramy sieciowe ([Internet Gateway](Internet%20Gateway.md), [NAT Gateway](NAT%20devices.md#NAT%20Gateway)).

VPC oferuje m.in.:

- wystawianie pewnych obszarów dostępnych z Internetu, np. serwer WWW,
- translacje adresów sieciowych lub [NAT](../Network/NAT.md) dla tych prywatnych podsieci,
- łączenie VPC między sobą,
- prywatne połączenia z wieloma usługami AWS.

Możesz mieć pewność, że wrażliwa aplikacja nie musi wysyłać ruchu przez Internet, tylko może pozostać w obrębie VPC, nawet jeśli korzysta z konkretnych usług AWS.

# Trasowanie VPC

![](VPC%20Route%20table.md#Trasowanie%20VPC)

# VPC Endpoint

[źródło](https://rozchmurzeni.pl/vpc-endpoint/)

**VPC Endpoint** to usługa pozwalająca na wpięcie do naszego VPC publicznych usług AWS. Przykładem takich usług są: S3, DynamoDB, API Gateway, Systems Manager, etc. Pod spodem **endpointy** oparte są na [PrivateLink](PrivateLink.md), dzięki czemu cały ruch nie opuszcza sieci AWS. 
