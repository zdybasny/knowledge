#AWS #network

Jednym z aspektów skalowalności (elastycznoći) jest sposób, w jaki kierujemy użytkowników do odpowiedniej infrastruktury, za co odpowiedzialny jest Elastic Load Balancing. ELB umożliwia dystrybucję ruchu do wielu miejsc docelowych, np. oparciu o obciążenie każdego z dostępnych serwerów. Obsługuje jedną lub więcej [[AWS locations#Availability zone|Availaility zone]]s w ramach [[AWS locations#Region|regionu]].

Domyślnie integruje się z [[EC2]], [[Elastic Container Services]].

Istnieją trzy rodzaje load balancerów:

- [[# Application Load Balancer]] (ALB) - do trasowania ruchu HTTP/HTTPS (lub warstwy 7)

- [[# Network Load Balancer]] (ELB) - do trasowania ruchu TCP lub UDP (lub warstwy 4)

- [[# Classic Load Balancer]] (czasem określane po prostu jako ELB) - do trasowania ruchu TCP

- [[# Gateway Load Balancer]] #TODO %%czy GLB należy do ELASTIC Load balancerów?%%

Usługi Amazon ECS hostowane na instancjach Amazon EC2 obsługują typy balansera obciążenia [[# Application Load Balancer]], [[# Network Load Balancer]] i [[# Classic Load Balancer]]. Usługi Amazon ECS hostowane na AWS Fargate obsługują tylko typy [[# Application Load Balancer]] i [[# Network Load Balancer]].

# Application Load Balancer

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)

**Application Load Balancer** podejmuje decyzje dotyczące routingu w warstwie aplikacji (HTTP/HTTPS), obsługuje routing oparty na ścieżkach i może kierować żądania do jednego lub więcej portów na każdej instancji kontenera w klastrze.

## Subnets i Availabiligty zones dla ALB
[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/application-load-balancers.html#subnets-load-balancer)

Kiedy tworzysz Application Load Balancer, musisz określić jeden z następujących typów podsieci: 

- Availability Zone,
- Local Zone,
- Outpost.

### Availability Zones

Należy wybrać co najmniej dwie podsieci strefy dostępności. Obowiązują następujące ograniczenia:

- Każda podsieć musi pochodzić z innej strefy Availability Zone.

- Aby upewnić się, że Twój load balancer może się prawidłowo skalować, sprawdź, czy każda podsieć strefy dostępności dla Twojego load balancera ma blok CIDR z co najmniej /27 bitmaską (na przykład 10.0.0.0/27) i co najmniej 8 wolnych adresów IP na podsieć. Twój load balancer używa tych adresów IP do nawiązywania połączeń z celami. W zależności od profilu ruchu, load balancer może skalować się wyżej i zużywać maksymalnie do 100 adresów IP rozłożonych na wszystkie włączone podsieci.

### Local Zone

Można określić jedną lub więcej podsieci strefy lokalnej. Obowiązują następujące ograniczenia:

- Nie możesz używać [[AWS WAF]] z load balancerem.

- Nie można używać funkcji [[Lambda]] jako celu.

## Security group dla ALB

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-update-security-groups.html)

Musisz upewnić się, że Twój load balancer może komunikować się z zarejestrowanymi celami zarówno na porcie listenera jak i porcie kontroli zdrowia. Za każdym razem, gdy dodajesz [[#Listener]] do swojego load balancera lub aktualizujesz port kontroli zdrowia dla grupy docelowej używanej przez load balancer do trasowania żądań, musisz sprawdzić, czy [[Security group]] skojarzone z load balancerem zezwalają na ruch na nowym porcie w obu kierunkach. Jeśli tak nie jest, można edytować reguły dla aktualnie powiązanych grup zabezpieczeń lub powiązać inne grupy zabezpieczeń z load balancerem. W [[VPC]] podajesz [[Security group]] dla load balancera, co pozwala Ci wybrać porty i protokoły, które mają być dozwolone. Na przykład, możesz otworzyć połączenia Internet Control Message Protocol (ICMP), aby load balancer odpowiadał na żądania ping (jednak żądania ping nie są przekazywane do żadnych instancji).

## Komponenty ALB

![[ALB components.png]]

**Load balancer** rozprowadza przychodzący ruch aplikacji po wielu celach, takich jak instancje EC2, w wielu strefach dostępności.

**<u>Listener</u>** sprawdza żądania połączeń od klientów, używając protokołu i portu, które konfigurujesz. **Reguły**, które definiujesz dla listenera określają, w jaki sposób load balancer kieruje żądania do zarejestrowanych celów. Każda reguła składa się z priorytetu, jednej lub więcej akcji oraz jednego lub więcej warunków. Gdy warunki dla danej reguły są spełnione, wówczas wykonywane są jej akcje. Musisz zdefiniować domyślną regułę dla każdego listenera i możesz opcjonalnie zdefiniować dodatkowe reguły.

**<u>Target group</u>** kieruje żądania do jednego lub więcej zarejestrowanych celów, takich jak instancje [[EC2]], używając protokołu i numeru portu, który określisz. Możesz zarejestrować jeden cel z wieloma grupami docelowymi. _Więcej o [[Target]]_

**<u>Health ckeck</u>** możesz skonfigurować  na podstawie każdej **grupy docelowej**. Health ckecki wykonywane są na wszystkich **celach** zarejestrowanych w **grupie docelowej**, która jest określona w regule **listenera** dla Twojego load balancera. Możesz skonfigurować health ckecki, które są używane do monitorowania zdrowia zarejestrowanych celów, tak aby load balancer mógł wysyłać żądania tylko do zdrowych celów.

### Listener

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-listeners.html)
#TODO

### Target group

[źródło](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)

#### Typ taget group

- Instances
  - Obsługuje load balancing do instancji w ramach określonego VPC.

- IP addresses
  - Wspiera load balancing do VPC i zasobów lokalnych.
  - Ułatwia routing do wielu adresów IP i interfejsów sieciowych na tej samej instancji.
  - Oferuje elastyczność w architekturach opartych na mikroserwisach, upraszczając komunikację między aplikacjami.

- [[Lambda function]]
  - Ułatwia routing do pojedynczej funkcji Lambda.
  - Dostępna tylko dla Application Load Balancerów.

##### Intanceses vs IP addresses

Instances:

- Typ docelowy instancji jest ograniczony tylko do niektórych instancji. Powinien to być Twój domyślny wybór podczas load balancingu instancji. Na przykład, jeśli masz instancje w grupie autoskalowania (ASG), ASG może automatycznie zarejestrować twoje instancje z twoim load balancerem. Nie możesz tego zrobić dla typów docelowych IP.

IP adresses:

- Cel nie musi być instancją - wszystko z prywatnym adresem IP będzie działać, w tym wewnętrzny load balance, prywatna usługa VPC, kontenery Fargate, bazy danych, serwery on-premise przez VPN.
- Cel może być w innym regionie, tak długo, jak masz cross-region peering między swoimi VPC masz wiele interfejsów sieciowych na swojej instancji, więc możesz rozdzielać ruch pomiędzy nimi, np. różne aplikacje na jednej instancji są przypisane do różnych interfejsów.
- Każdy interfejs może być powiązany z inną grupą docelową.

### Helth check

#TODO

%%- content base routing

- health checks
- sticky sessions
- LB algorythm%%

## Usługi kompatybilne

Application Load Balancer

- Offers the flexibility for a Network Load Balancer to accept and route TCP requests within a specific VPC.
- Facilitates using static IP addresses and PrivateLink with an Application Load Balancer.

# Network Load Balancer

#TODO

%%- zonal isolation

- health checks
- sticky sessions
- long live connect%%

# Gateway Load Balancer

#TODO

%%- tcp/udp based connect

- zonal isolation
- health
- long live connectio n%%

# Classic Load Balancer

#TODO  ? outdates ?
