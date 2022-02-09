#AWS #management #autoscaling

AWS Auto Scaling pozwala budować plany skalowania ([Scaling plan](#Scaling%20plan)) dla różnych [zasobów skalowalnych](#Autoskalowanie%20zasobów) AWS, w tym:

- instancje [EC2](EC2.md),
- [Spot Fleets](EC2.md#Spot%20Fleets),
- zadania [Elastic Container Services](Elastic%20Container%20Services.md),
- tabele i indeksy [DynamoDB](DynamoDB.md),
- repliki [RDS](RDS.md) (w tym [Aurora](Aurora.md)).

AWS Auto Scaling sprawia, że skalowanie jest proste dzięki rekomendacjom, które pozwalają na optymalizację wydajności, kosztów lub równowagi między nimi.

##### Spis treści

- [Scaling plan](#Scaling%20plan)
- [Autoskalowanie zasobów](#Autoskalowanie%20zasobów)
  - [EC2 Auto Scaling](#EC2%20Auto%20Scaling)
  - [Skalowanie Spot Fleet](#Skalowanie%20Spot%20Fleet)
  - [Skalowanie Elastic Container Service](#Skalowanie%20Elastic%20Container%20Service)
  - [Skalowanie Aurora](#Skalowanie%20Aurora)
  - [Skalowanie DynamoDB](#Skalowanie%20DynamoDB)

# Scaling plan

Plana skalowania pozwalają skonfigurować automatyczne skalowanie dla powiązanych lub skojarzonych [zasobów skalowalnych](#Autoskalowanie%20zasobów) w ciągu kilku minut.

Np. można użyć znaczników do grupowania zasobów w kategoriach takich jak prod/test/dev. Następnie można wyszukać i skonfigurować plany skalowania dla zasobów skalowalnych, które należą do każdej kategorii.
Lub, jeśli Twoja infrastruktura chmury zawiera AWS CloudFormation, możesz zdefiniować szablony stosów do wykorzystania przy tworzeniu kolekcji zasobów.

# Autoskalowanie zasobów

## EC2 Auto Scaling

[EC2 Auto Scaling](EC2%20Auto%20Scaling.md) zapewnia uruchomienie pożądanej liczby [EC2](EC2.md), nawet jeśli jedna z nich ulegnie awarii, oraz umożliwia automatyczne zwiększanie lub zmniejszanie liczby instancji w miarę zmian zapotrzebowania na nie.

## Skalowanie Spot Fleet

#TODO

## Skalowanie Elastic Container Service

#TODO

## Skalowanie RDS

[RDS Storage Auto Scaling](RDS%20Storage%20Auto%20Scaling.md) stale monitoruje rzeczywiste zużycie pamięci masowej i skaluje pojemność automatycznie, gdy rzeczywiste wykorzystanie zbliża się do dostarczonej pojemności pamięci masowej.

Nie ma żadnych dodatkowych kosztów dla RDS Storage Auto Scaling. Płacisz tylko za zasoby RDS potrzebne do uruchomienia aplikacji.

# Application Auto Scaling

[Application Auto Scaling](Application%20Auto%20Scaling.md)

#TODO

## Skalowanie DynamoDB

#TODO
