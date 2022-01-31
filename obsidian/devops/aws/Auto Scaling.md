#AWS #management #autoscaling

AWS Auto Scaling pozwala budować plany skalowania ([[#Scaling plan]]) dla różnych [[#Autoskalowanie zasobów|zasobów skalowalnych]] AWS, w tym:

- instancje [[EC2]],
- [[EC2#Spot Fleets|Spot Fleets]],
- zadania [[Elastic Container Services]],
- tabele i indeksy [[DynamoDB]],
- repliki [[RDS]] (w tym [[Aurora]]).

AWS Auto Scaling sprawia, że skalowanie jest proste dzięki rekomendacjom, które pozwalają na optymalizację wydajności, kosztów lub równowagi między nimi.

##### Spis treści

- [[#Scaling plan]]
- [[#Autoskalowanie zasobów]]
  - [[#EC2 Auto Scaling]]
  - [[#Skalowanie Spot Fleet]]
  - [[#Skalowanie Elastic Container Service]]
  - [[#Skalowanie Aurora]]
  - [[#Skalowanie DynamoDB]]

# Scaling plan

Plana skalowania pozwalają skonfigurować automatyczne skalowanie dla powiązanych lub skojarzonych [[#Autoskalowanie zasobów|zasobów skalowalnych]] w ciągu kilku minut.

Np. można użyć znaczników do grupowania zasobów w kategoriach takich jak prod/test/dev. Następnie można wyszukać i skonfigurować plany skalowania dla zasobów skalowalnych, które należą do każdej kategorii.
Lub, jeśli Twoja infrastruktura chmury zawiera AWS CloudFormation, możesz zdefiniować szablony stosów do wykorzystania przy tworzeniu kolekcji zasobów.

# Autoskalowanie zasobów

## EC2 Auto Scaling

[[EC2 Auto Scaling]] zapewnia uruchomienie pożądanej liczby [[EC2]], nawet jeśli jedna z nich ulegnie awarii, oraz umożliwia automatyczne zwiększanie lub zmniejszanie liczby instancji w miarę zmian zapotrzebowania na nie.

## Skalowanie Spot Fleet

#TODO

## Skalowanie Elastic Container Service

#TODO

## Skalowanie RDS

[[RDS Storage Auto Scaling]] stale monitoruje rzeczywiste zużycie pamięci masowej i skaluje pojemność automatycznie, gdy rzeczywiste wykorzystanie zbliża się do dostarczonej pojemności pamięci masowej.

Nie ma żadnych dodatkowych kosztów dla RDS Storage Auto Scaling. Płacisz tylko za zasoby RDS potrzebne do uruchomienia aplikacji.

# Application Auto Scaling

[[Application Auto Scaling]]

#TODO

## Skalowanie DynamoDB

#TODO
