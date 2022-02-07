#AWS #network

Możesz użyć urządzenia [[NAT]], aby umożliwić instancjom w prywatnych podsieciach łączenie się z internetem, innymi [[VPC]]. Urządzenie NAT zamienia źródłowy adres IPv4 instancji na adres urządzenia NAT. Podczas wysyłania ruchu odpowiedzi do instancji [[EC2]], urządzenie NAT tłumaczy adresy z powrotem na oryginalne źródłowe adresy IPv4.

Instancje [[EC2]] te mogą komunikować się z usługami spoza [[VPC]], ale nie mogą otrzymywać niechcianych żądań połączenia.

Możesz użyć zarządzanego urządzenia NAT oferowanego przez AWS, zwanego [[#NAT Gateway]] lub możesz stworzyć własne urządzenie NAT na instancji [[EC2]], zwanej [[#NAT Inctance]].

# NAT Gateway

Zalecamy korzystanie z bram NAT, ponieważ zapewniają one lepszą dostępność i przepustowość oraz wymagają mniej nakładu pracy.

Kiedy tworzysz bramę NAT, określasz jeden z następujących typów łączności:

- **prywatny** (_domyślnie_)-  Instancje [[EC2]] w prywatnych [[VPC Subnet]] mogą łączyć się z Internetem przez publiczny NAT Gateway, ale nie mogą otrzymywać niechcianych połączeń przychodzących z Internetu. Należy przypisać [[Elastic IP]] do bramy NAT podczas jej tworzenia.

- **prywatny** - Instancje [[EC2]] w prywatnych [[VPC Subnet]] mogą łączyć się z innymi [[VPC]] poprzez prywatną bramę NAT. Ruch z bramy NAT może być kierowany przez bramę tranzytową lub wirtualną bramę prywatną. Nie można powiązać elastycznego adresu IP z prywatną bramą NAT.

# NAT Instance

#TODO

---

# See also:

- [[Internet Gateway]]
