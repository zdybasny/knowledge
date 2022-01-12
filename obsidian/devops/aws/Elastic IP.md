#AWS #network #computing

# Przedstawienie

Elastic IP to statyczny adres IPv4[^1] osiągalny z Internetu przeznaczony do dynamicznego przetwarzania w chmurze. Statyczność oznacza, że nie zmienia się w czasie. Elastic IP jest przypisany do Twojego konta AWS i jest Twój, dopóki go nie zwolnisz.

Używając adresu Elastic IP, możesz zamaskować awarię [[EC2]] lub oprogramowania poprzez szybkie przemapowanie adresu na inną [[EC2]] na Twoim koncie. Alternatywnie, możesz podać adres Elastic IP w rekordzie [[DNS]] dla swojej domeny, tak aby Twoja domena wskazywała na Twoją [[EC2]].

Aby skorzystać z Elastic IP, należy przypisać go do konta, a następnie skojarzyć z [[EC2]] lub [[Elastic network interface]]. Jeśli jest skojarzony z instancją [[EC2]], jest on również skojarzony z jej głównym interfejsem. Jeśli jest skojarzony z [[Elastic network interface]], który jest dołączony do [[EC2]], jest on również powiązany z tą instancją.

# Limity

[źródło](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html)

Limit (quotas) adresów IP na region to: 5.
Ten limit dotyczy indywidualnych kont AWS VPC i współdzielonych VPC.

> Aby przydzielić adres IP do większej liczby [[EC2]], należy przypisać go to NAT

---

# See also:

[^1]: Obecnie AWS nie świadczy usługi Elastic IP dla IPv6.
