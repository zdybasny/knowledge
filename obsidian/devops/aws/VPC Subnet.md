#AWS #network

# Trasowanie Podsieci

![](VPC%20Route%20table.md#Trasowanie%20Podsieci)

# Strefy dostępności

VPC rozciąga się na wszystkie [AWS locations](AWS%20locations.md)s w regionie.

Po utworzeniu [VPC](VPC.md) można dodać jedną lub więcej podsieci w każdej [AWS locations](AWS%20locations.md).

Każda podsieć musi znajdować się w całości w jednej [AWS locations](AWS%20locations.md) i nie może rozciągać się na inne strefy. Opcjonalnie można dodać podsieci w strefie lokalnej ([Local zone](AWS%20locations.md#Local%20zone)).

Opcjonalnie można przypisać blok IPv6 CIDR do swojego [VPC](VPC.md) i do [VPC Subnet](VPC%20Subnet.md).

# Nework ACL dla podsieci

[Network ACL](Network%20ACL.md)s kontrolują ruch przychodzący do i wychodzący z VPC Subnet.

Każda podsieć z założenia musi być powiązana z NACL. Każda utworzona podsieć jest automatycznie kojarzona z domyślnym [Network ACL](Network%20ACL.md) dla [VPC](VPC.md)

Więcej o regułach w rodziale [Reguły](Network%20ACL.md#Reguły)
