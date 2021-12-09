#AWS #network

# Trasowanie Podsieci

![[VPC Route table#Trasowanie Podsieci]]

# Strefy dostępności

VPC rozciąga się na wszystkie [[AWS locations]]s w regionie.

Po utworzeniu [[VPC]] można dodać jedną lub więcej podsieci w każdej [[AWS locations]].

Każda podsieć musi znajdować się w całości w jednej [[AWS locations]] i nie może rozciągać się na inne strefy. Opcjonalnie można dodać podsieci w strefie lokalnej ([[AWS locations#Local zone]]).

Opcjonalnie można przypisać blok IPv6 CIDR do swojego [[VPC]] i do [[VPC Subnet]].

# Nework ACL dla podsieci

[[Network ACL]]s kontrolują ruch przychodzący do i wychodzący z VPC Subnet.

Każda podsieć z założenia musi być powiązana z NACL. Każda utworzona podsieć jest automatycznie kojarzona z domyślnym [[Network ACL]] dla [[VPC]]

Więcej o regułach w rodziale [[Network ACL#Reguły]]
