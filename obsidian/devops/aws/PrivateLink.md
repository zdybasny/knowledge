#AWS #network 

[źródło](https://docs.aws.amazon.com/vpc/latest/privatelink/endpoint-services-overview.html) 

**AWS PrivateLink** to wysoce dostępna, skalowalna technologia, która umożliwia prywatne połączenie [[VPC]] z:

- usługami AWS, 
- usługami hostowanymi przez inne konta AWS ([[#VPC endpoint service]]),
- usługami  partnerskimi obsługujących AWS Marketplace. 

Aby komunikować się z usługą nie trzeba używać:
- [[Internet Gateway]], 
- [[NAT devices]], 
- publicznego adresu IP, 
- połączenia [[Direct Connect]] 
- lub połączenia AWS Site-to-Site VPN.

W związku z tym, kontrolujesz konkretne API endpoints, witryny i usługi, które są osiągalne z [[VPC]].

Możesz stworzyć własną usługę punktu końcowego [[VPC]], zasilaną przez **AWS PrivateLink**, i umożliwić innym klientom AWS dostęp do swojej usługi.

##### Spis treści

- [[#VPC endpoint]]
  - [[#Gateway endpoint]]
  - [[#Interface endpoint]]
  - [[#Gateway Load Balancer endpoint]]
- [[#VPC endpoint service]]
- [[#See also]]

# VPC endpoint

[źródło](https://rozchmurzeni.pl/vpc-endpoint/)

**VPC Endpoint** to usługa pozwalająca na wpięcie do naszego VPC publicznych usług AWS. Przykładem takich usług są: S3, DynamoDB, API Gateway, Systems Manager, etc. Pod spodem **endpointy** oparte są na [[PrivateLink]], dzięki czemu cały ruch nie opuszcza sieci AWS. 

Powoduje to, że nasze zasoby nie muszą być adresowalne publicznie ani nawet nie muszą mieć połączenia z Internetem. W niektórych przypadkach pomogą także ze zmniejszeniem kosztów!

Podczas wykorzystania VPC Endpointów nie musimy zajmować się tworzeniem odpowiedniej liczby instancji, tak by mogły obsłużyć ruch w naszej sieci. Jednocześnie nie musimy zaprzątać głowy zapewnieniem zastępczych tras czy instancji w przypadku awarii. Wszystko to dzięki temu, że VPC Endpoint jest usługą o wysokiej dostępności, redundancji oraz jest skalowalna w poziomie.

## Gateway endpoint

Ten rodzaj **endpointów** pozwala na dostęp do dwóch usług:

- [[S3]]
- [[DynamoDB]]. 
 
Tworzy się je na poziomie [[VPC]], w jednym [[VPC]] może znajdować się kilka **endpointów** do tej samej usługi. Dzięki temu możemy korzystać z różnych **polityk dostępu** dla każdego z nich.

Ruch do takich endpointów kieruje się za pomocą [[VPC Route table]]. Daje to w prosty sposób możliwość wyboru, które z naszych [[VPC Subnet]] mają korzystać z serwisu przez określony **VPC Endpoint**, a które nie.

## Interface endpoint

Ten rodzaj **endpointów** pozwala na dostęp do dużej liczby usług, m.in.:

- [[API Gateway]], 
- [[SNS]], 
- [[SQS]],
- Systems Manager.

Pełna lista wspieranych usług dostępna jest w dokumentacji AWS pod [tym adresem](https://docs.aws.amazon.com/vpc/latest/privatelink/integrated-services-vpce-list.html).

**Interface endpoint** może otrzymać ruch na jeden z dwóch sposobów:

- wykorzystanie prywatnego [[DNS]] w [[VPC]] – w tym rozwiązaniu cały ruch z [[VPC]] przekierowany jest przez ten **endpoint**; nie wymaga żadnych zmian w kodzie);
- korzystanie z prywatnych adresów **endpointów** -  klienci używają ich zamiast domyślnych adresów usług lub załączają nagłówek `Host` (tak jest w przypadku endpointa dla [[API Gateway]]); wymagana jest rekonfiguracja klientów.

## Gateway Load Balancer endpoint

[źródło](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-gateway-load-balancer.html)

#TODO 

# VPC endpoint service

#TODO 

---
# See also

- [[VPC]]