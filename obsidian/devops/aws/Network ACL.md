#AWS #network #secutiry 

# Przedstawienie

Sieciowe_listy ACL (NACL) kontrolują ruch przychodzący do i wychodzący z [[VPC Subnet]].

W większości przypadków [[Security group]] zaspokajają potrzeby, ale można również użyć NACL w celu uzyskania dodatkowej warstwy zabezpieczeń dla [[VPC]].

Każda [[VPC Subnet]] z założenia musi być powiązana z NACL. Każda utworzona [[VPC Subnet]] jest automatycznie kojarzona z domyślnym NACL dla [[VPC]]. Można zmienić to skojarzenie, a także zawartość domyślnej NACL.

Można utworzyć dziennik przepływów ([[VPC Flow Logs]]) w [[VPC]] lub [[VPC Subnet]] w celu przechwytywania ruchu przychodzącego i wychodzącego z interfejsów sieciowych w [[VPC]] lub [[VPC Subnet]]. Można również utworzyć [[VPC Flow Logs]] na pojedynczym [[Elastic network interface]]. Dzienniki przepływów są publikowane w [[CloudWatch#CloudWatch Logs]] lub [[S3]].

# Podstawy

[źródło](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-basics)

Poniżej przedstawiamy podstawowe rzeczy, które należy wiedzieć o NACL:

- [[VPC]] jest automatycznie dostarczane z domyślnym NACL, który można modyfikować. **Domyślnie zezwala ona na cały przychodzący i wychodzący ruch** IPv4 oraz, jeśli ma to zastosowanie, ruch IPv6.

- Można utworzyć własny NACL i powiązać go z [[VPC Subnet]]. **Domyślnie każda niestandardowa NACL odmawia przyjęcia całego ruchu** przychodzącego i wychodzącego do momentu dodania reguł.

- Każda [[VPC Subnet]] musi być powiązana z NACL. Jeśli nie skojarzysz podsieci z NACL, zostanie ona automatycznie skojarzona z domyślną NACL.

- Można przypisać NACL do wielu [[VPC Subnet]]s. Jednak w danym momencie jenda [[VPC Subnet]] może być skojarzona tylko z jedną NACL. Po skojarzeniu NACL z [[VPC Subnet]] poprzednie skojarzenie jest usuwane.

- NACL zawiera numerowaną listę reguł. Reguły są oceniane w kolejności, zaczynając od najniższej, aby określić, czy ruch jest dozwolony do lub z [[VPC Subnet]] skojarzonej z NACL.
  Najwyższy numer, jaki można użyć dla reguły to 32766. Zaleca się tworzenie reguł w przyrostach (np. co 10 lub 100), aby można było później dodawać nowe reguły w razie potrzeby.*

- NACL ma oddzielne reguły przychodzące i wychodzące, a każda z nich może zezwalać lub odmawiać dostępu do ruchu.

- NACL są bezstanowe, co oznacza, że odpowiedzi na dozwolony ruch przychodzący podlegają regułom dla ruchu wychodzącego (i na odwrót).

## Limity

Istnieją limity (quotas) liczby NACL na VPC oraz liczby reguł na listę NACL. Więcej informacji na ten temat można znaleźć w części [Amazon VPC quotas](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html).

# Reguły

[źródło](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-rules)

Można dodawać i usuwać reguły z domyślnej listy ACL sieciowej lub tworzyć dodatkowe listy ACL sieciowe dla VPC. Po dodaniu lub usunięciu reguł z listy ACL sieciowej, zmiany są automatycznie stosowane do podsieci, z którymi jest ona powiązana.

Poniżej przedstawiono części składowe reguły NACL:

- **Rule number** - Reguły są ewaluaowane, zaczynając od reguły o najniższym numerze. Gdy tylko reguła dopasuje się do ruchu, jest ona stosowana niezależnie od reguł o wyższym numerze, które mogą być z nią sprzeczne.

- **Type** - Typ ruchu (na przykład SSH). Można również określić cały ruch lub niestandardowy zakres.

- **Protocol** - Można określić dowolny protokół, który ma standardowy numer protokołu. Więcej informacji: [Protocol Numbers](http://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml).

- **Port range** - Port nasłuchiwania lub zakres portów dla ruchu sieciowego. Na przykład 80 dla ruchu HTTP.

- **Source** - [Tylko reguły przychodzące] Źródło ruchu (zakres [[CIDR]])

- **Destination** - [Tylko reguły wychodzące] Miejsce docelowe dla ruchu (zakres [[CIDR]]).

- **Allow/Deny** - Określenie, czy _zezwalać_ na określony ruch, czy go _odrzucać_.

> Jeśli dodasz regułę za pomocą wiersza poleceń lub API [[EC2]], zakres [[CIDR]] zostanie automatycznie zmodyfikowany do postaci kanonicznej. Na przykład, jeśli podasz `100.68.0.18/18` jako zakres [[CIDR]], utworzy to regułę z zakresem `100.68.0.0/18`.

# Porównianie network ACL i security group

[źródło](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Security.html#VPC_Security_Comparison)

| [[Security group]]                                                                                                                                      | Network ACL                                                                                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Działa na poziomie [[EC2]]                                                                                                                              | Działa na poziomie [[VPC Subnet]]                                                                                                                                                           |
| Obsługuje tylko reguły zezwalania (allow)                                                                                                               | Obsługuje reguły zezwalania i odmowy (allow & deny)                                                                                                                                         |
| Stanowy: Ruch zwrotny jest automatycznie dozwolony, niezależnie od jakichkolwiek reguł.                                                                 | Bezstanowe: Ruch zwrotny musi być jawnie dozwolony przez reguły.                                                                                                                            |
| Oceniamy wszystkie reguły przed podjęciem decyzji o zezwoleniu na ruch.                                                                                 | Przetwarzamy reguły w kolejności, zaczynając od reguły o najniższym numerze, podczas podejmowania decyzji o zezwoleniu na ruch.                                                             |
| Stosuje się do [[EC2]] tylko wtedy, gdy ktoś określi [[Security group]] podczas uruchamiania [[EC2]] lub skojarzy [[Security group]] z [[EC2]] później. | Automatycznie stosuje się do wszystkich [[EC2]] w [[VPC Subnet]]s, z którymi jest powiązana (dlatego zapewnia dodatkową warstwę obrony, jeśli reguły [[Security group]]s są zbyt liberalne) |

Poniższy diagram ilustruje warstwy zabezpieczeń zapewnianych przez [[Security group]]s i NACL:

![[VPC diagram.png]]

---

# See also:

- [Network ACLs on AWS](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html)
- [[VPC Subnet]]
- [[Security group]]
- [[CIDR]]
