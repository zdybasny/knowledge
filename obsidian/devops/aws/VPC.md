#AWS #network

# Amazon VPC

Amazon VPC to wirtualna chmura prywatna, która jest logicznie odizolowana od publicznej części. Można na w niej uruchamiać usługi i zasoby AWS.

Umożliwia to posiadanie wirtualnej sieci (VPN), którą definiujesz i możesz konfigurować. VPC obsługuje zarówno IPv4 jak i IPv6.

Można skonfigurować:

- zakres adresów IP ([[CIDR]]),
- podsieci ([[VPC Subnet]]),
- tabele tras ([[VPC Route table]]),
- bramy sieciowe ([[Internet Gateway]], [[NAT devices#NAT Gateway|NAT Gateway]]).

VPC oferuje m.in.:

- wystawianie pewnych obszarów dostępnych z Internetu, np. serwer WWW,
- translacje adresów sieciowych lub [[NAT]] dla tych prywatnych podsieci,
- łączenie VPC między sobą,
- prywatne połączenia z wieloma usługami AWS.

Możesz mieć pewność, że wrażliwa aplikacja nie musi wysyłać ruchu przez Internet, tylko może pozostać w obrębie VPC, nawet jeśli korzysta z konkretnych usług AWS.

# Trasowanie VPC

![[VPC Route table#Trasowanie VPC]]

# VPC Endpoint

[źródło](https://rozchmurzeni.pl/vpc-endpoint/)

**VPC Endpoint** to usługa pozwalająca na wpięcie do naszego VPC publicznych usług AWS. Przykładem takich usług są: S3, DynamoDB, API Gateway, Systems Manager, etc. Pod spodem **endpointy** oparte są na [[PrivateLink]], dzięki czemu cały ruch nie opuszcza sieci AWS. 
