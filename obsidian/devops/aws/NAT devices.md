#AWS #network

Możesz użyć urządzenia [NAT](../Network/NAT.md), aby umożliwić instancjom w prywatnych podsieciach łączenie się z internetem, innymi [VPC](VPC.md). Urządzenie NAT zamienia źródłowy adres IPv4 instancji na adres urządzenia NAT. Podczas wysyłania ruchu odpowiedzi do instancji [EC2](EC2.md), urządzenie NAT tłumaczy adresy z powrotem na oryginalne źródłowe adresy IPv4.

Instancje [EC2](EC2.md) te mogą komunikować się z usługami spoza [VPC](VPC.md), ale nie mogą otrzymywać niechcianych żądań połączenia.

Możesz użyć zarządzanego urządzenia NAT oferowanego przez AWS, zwanego [NAT Gateway](#NAT%20Gateway) lub możesz stworzyć własne urządzenie NAT na instancji [EC2](EC2.md), zwanej [NAT Inctance](#NAT%20Inctance).

# NAT Gateway

Zalecamy korzystanie z bram NAT, ponieważ zapewniają one lepszą dostępność i przepustowość oraz wymagają mniej nakładu pracy.

Kiedy tworzysz bramę NAT, określasz jeden z następujących typów łączności:

- **prywatny** (_domyślnie_)-  Instancje [EC2](EC2.md) w prywatnych [VPC Subnet](VPC%20Subnet.md) mogą łączyć się z Internetem przez publiczny NAT Gateway, ale nie mogą otrzymywać niechcianych połączeń przychodzących z Internetu. Należy przypisać [Elastic IP](Elastic%20IP.md) do bramy NAT podczas jej tworzenia.

- **prywatny** - Instancje [EC2](EC2.md) w prywatnych [VPC Subnet](VPC%20Subnet.md) mogą łączyć się z innymi [VPC](VPC.md) poprzez prywatną bramę NAT. Ruch z bramy NAT może być kierowany przez bramę tranzytową lub wirtualną bramę prywatną. Nie można powiązać elastycznego adresu IP z prywatną bramą NAT.

# NAT Instance

#TODO

---

# See also:

- [Internet Gateway](Internet%20Gateway.md)
