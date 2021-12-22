#AWS #network

# Przedstawienie

**Grupy bezpieczeństwa** działają jak firewall, kontrolując ruch <mark style="background-color: #000082">przychodzący</mark> (**inbound**) i <mark style="background-color: #008200">wychodzący</mark> (**outbound**) z [[EC2]].

Jedna [[EC2]] w sieci [[VPC]] może mieć przypisanych do pięciu security groups. Każda [[EC2]] w podsieci w [[VPC]] może być przypisana do innego zestawu security groups. W przypadku niepodania security group przy tworzeniu [[EC2]], zostaje ona automatycznie przypisana do domyślnej security group dla [[VPC]].

Security groups działają na poziomie instancji, a nie na poziomie podsieci. Dlatego każda instancja w podsieci w VPC może być przypisana do innego zestawu security groups.

# Podstawy

[źródło](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#VPCSecurityGroups)

Poniżej przedstawiono charakterystykę security groups:

- Security group może być używana tylko w [[VPC]], który został określony podczas tworzenia grupy.

- Reguły security groups umożliwiają filtrowanie ruchu na podstawie protokołów i numerów portów.

- Można określić reguły zezwalania (**allow**), ale nie reguły odmowy (**deny**).

- Można określić oddzielne reguły dla ruchu <mark style="background-color: #000082">przychodzącego</mark> (**inbound**) i <mark style="background-color: #008200">wychodzącego</mark> (**outbound**).

- Security groups są stanowe.
  - Jeśli wyślesz żądanie z [[EC2]] (<mark style="background-color: #008200">ruch wychodzący</mark>), to <mark style="background-color: #000082">ruch odpowiedzi</mark> dla tego żądania jest dozwolony niezależnie od reguł security groups dla <mark style="background-color: #000082">ruchu przychodzącego</mark>.

  - <mark style="background-color: #008200">Odpowiedzi</mark> na dozwolony <mark style="background-color: #000082">ruch przychodzący</mark> są dozwolone do przepływu na zewnątrz, niezależnie od reguł <mark style="background-color: #008200">ruchu wychodzącego</mark>.

- Podczas pierwszego tworzenia security group nie ma ona żadnych reguł przychodzących. Dlatego <mark  style="background-color: #000082">ruch przychodzący</mark> z innego hosta do [[EC2]] nie jest dozwolony, dopóki nie dodasz reguł przychodzących do security group.

- Domyślnie security group zawiera regułę wychodzącą, która zezwala na cały <mark style="background-color: #008200">ruch wychodzący</mark>. Można usunąć tę regułę i dodać reguły wychodzące, które zezwalają tylko na określony <mark style="background-color: #008200">ruch wychodzący</mark>.

- Jeśli Twoja security group nie ma żadnych reguł <mark style="background-color: #008200">ruchu wychodzącego</mark>, żaden <mark style="background-color: #008200">ruch wychodzący</mark> pochodzący z [[EC2]] nie jest dozwolony.

- [[EC2]] powiązane z security group nie mogą ze sobą rozmawiać, chyba że zostaną dodane reguły zezwalające na ruch (wyjątek: domyślna security group ma takie reguły domyślnie).

- Security groups są powiązane z [[Elastic network interface]]. Po uruchomieniu [[EC2]] możesz zmienić security groups, które są z nią powiązane, co powoduje zmianę security groups powiązanych z podstawowym interfejsem sieciowym (`eth0`). Możesz też określić lub zmienić security groups powiązane z dowolnym innym [[Elastic network interface]]. Domyślnie, gdy tworzysz [[Elastic network interface]], jest on skojarzony z domyślną security group dla [[VPC]], chyba że określisz inną security group.

## Limity

Istnieją limity dotyczące liczby security groups, które można utworzyć w ramach jednego VPC, liczby reguł, które można dodać do każdej grupy, oraz liczby grup, które można powiązać z interfejsem sieciowym. Więcej informacji: [Amazon VPC quotas](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html).

# Porównianie network ACL i security group

![[Network ACL#Porównianie network ACL i security group]]

---

# See also:

- [Security groups in AWS doc](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)
- [[EC2]]
- [[Network ACL]]
- [[CIDR]]
