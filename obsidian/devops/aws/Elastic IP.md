#AWS #network

Elastic IP to statyczny adres IPv4[^1] osiągalny z Internetu przeznaczony do dynamicznego przetwarzania w chmurze. Statyczność oznacza, że nie zmienia się w czasie. Elastic IP jest przypisany do Twojego konta AWS i jest Twój, dopóki go nie zwolnisz.

Używając adresu Elastic IP, możesz zamaskować awarię [[EC2]] lub oprogramowania poprzez szybkie przemapowanie adresu na inną [[EC2]] na Twoim koncie. Alternatywnie, możesz podać adres Elastic IP w rekordzie [[DNS]] dla swojej domeny, tak aby Twoja domena wskazywała na Twoją [[EC2]].

Aby skorzystać z Elastic IP, należy przypisać go do konta, a następnie skojarzyć z [[EC2]] lub [[Elastic Network Interface]]. Jeśli jest skojarzony z instancją [[EC2]], jest on również skojarzony z jej głównym interfejsem. Jeśli jest skojarzony z [[Elastic Network Interface]], który jest dołączony do [[EC2]], jest on również powiązany z tą instancją.

##### Spis treści

- [[#Limity]]
- [[#Best practices]]
- [[#See also]]

# Limity

[źródło](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html)

Limit (quotas) adresów IP na region to: 5.
Ten limit dotyczy indywidualnych kont AWS VPC i współdzielonych VPC.

> Aby przydzielić adres IP do większej liczby [[EC2]], należy przypisać go to NAT

# Best practices

Lepiej unikać Elastic IP. Zamiast tego można:

- używać losowo przydzielanye publiczne IP i rejestrować je w DNS,
- użyć [[Elastic Load Balancing]], które w ogóle nie używa publicznego IP.

---

# See also

[^1]: Obecnie AWS nie świadczy usługi Elastic IP dla IPv6.
