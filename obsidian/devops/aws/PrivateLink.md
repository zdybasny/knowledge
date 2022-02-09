#AWS #network 

[źródło](https://docs.aws.amazon.com/vpc/latest/privatelink/endpoint-services-overview.html) 

**AWS PrivateLink** to wysoce dostępna, skalowalna technologia, która umożliwia prywatne połączenie [VPC](VPC.md) z:

- usługami AWS, 
- usługami hostowanymi przez inne konta AWS ([VPC endpoint service](#VPC%20endpoint%20service)),
- usługami  partnerskimi obsługujących AWS Marketplace. 

Aby komunikować się z usługą nie trzeba używać:
- [Internet Gateway](Internet%20Gateway.md), 
- [NAT devices](NAT%20devices.md), 
- publicznego adresu IP, 
- połączenia [Direct Connect](Direct%20Connect.md) 
- lub połączenia AWS Site-to-Site VPN.

W związku z tym, kontrolujesz konkretne API endpoints, witryny i usługi, które są osiągalne z [VPC](VPC.md).

Możesz stworzyć własną usługę punktu końcowego [VPC](VPC.md), zasilaną przez **AWS PrivateLink**, i umożliwić innym klientom AWS dostęp do swojej usługi.

##### Spis treści

- [VPC endpoint](#VPC%20endpoint)
  - [Gateway endpoint](#Gateway%20endpoint)
  - [Interface endpoint](#Interface%20endpoint)
  - [Gateway Load Balancer endpoint](#Gateway%20Load%20Balancer%20endpoint)
- [VPC endpoint service](#VPC%20endpoint%20service)
- [See also](#See%20also)

# VPC endpoint

[źródło](https://rozchmurzeni.pl/vpc-endpoint/)

**VPC Endpoint** to usługa pozwalająca na wpięcie do naszego VPC publicznych usług AWS. Przykładem takich usług są: S3, DynamoDB, API Gateway, Systems Manager, etc. Pod spodem **endpointy** oparte są na [PrivateLink](PrivateLink.md), dzięki czemu cały ruch nie opuszcza sieci AWS. 

Powoduje to, że nasze zasoby nie muszą być adresowalne publicznie ani nawet nie muszą mieć połączenia z Internetem. W niektórych przypadkach pomogą także ze zmniejszeniem kosztów!

Podczas wykorzystania VPC Endpointów nie musimy zajmować się tworzeniem odpowiedniej liczby instancji, tak by mogły obsłużyć ruch w naszej sieci. Jednocześnie nie musimy zaprzątać głowy zapewnieniem zastępczych tras czy instancji w przypadku awarii. Wszystko to dzięki temu, że VPC Endpoint jest usługą o wysokiej dostępności, redundancji oraz jest skalowalna w poziomie.

## Gateway endpoint

Ten rodzaj **endpointów** pozwala na dostęp do dwóch usług:

- [S3](S3.md)
- [DynamoDB](DynamoDB.md). 
 
Tworzy się je na poziomie [VPC](VPC.md), w jednym [VPC](VPC.md) może znajdować się kilka **endpointów** do tej samej usługi. Dzięki temu możemy korzystać z różnych **polityk dostępu** dla każdego z nich.

Ruch do takich endpointów kieruje się za pomocą [VPC Route table](VPC%20Route%20table.md). Daje to w prosty sposób możliwość wyboru, które z naszych [VPC Subnet](VPC%20Subnet.md) mają korzystać z serwisu przez określony **VPC Endpoint**, a które nie.

## Interface endpoint

Ten rodzaj **endpointów** pozwala na dostęp do dużej liczby usług, m.in.:

- [API Gateway](API%20Gateway.md), 
- [SNS](SNS.md), 
- [SQS](SQS.md),
- Systems Manager.

Pełna lista wspieranych usług dostępna jest w dokumentacji AWS pod [tym adresem](https://docs.aws.amazon.com/vpc/latest/privatelink/integrated-services-vpce-list.html).

**Interface endpoint** może otrzymać ruch na jeden z dwóch sposobów:

- wykorzystanie prywatnego [DNS](../network/DNS.md) w [VPC](VPC.md) – w tym rozwiązaniu cały ruch z [VPC](VPC.md) przekierowany jest przez ten **endpoint**; nie wymaga żadnych zmian w kodzie);
- korzystanie z prywatnych adresów **endpointów** -  klienci używają ich zamiast domyślnych adresów usług lub załączają nagłówek `Host` (tak jest w przypadku endpointa dla [API Gateway](API%20Gateway.md)); wymagana jest rekonfiguracja klientów.

## Gateway Load Balancer endpoint

[źródło](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-gateway-load-balancer.html)

#TODO 

# VPC endpoint service

#TODO 

---
# See also

- [VPC](VPC.md)