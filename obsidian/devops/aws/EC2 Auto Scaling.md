#AWS #management #autoscaling #compute 

EC2 Auto Scaling jest częścią usługi [[Auto Scaling]], która zapewnia uruchomienie pożądanej liczby [[EC2]], nawet jeśli jedna z nich ulegnie awarii, oraz umożliwia automatyczne zwiększanie lub zmniejszanie liczby instancji w miarę zmian zapotrzebowania na nie.

##### Spis treści

- [[#Auto Scaling Group]]
- [[#Szablon uruchomieniowy]]
- [[#Konfiguracja uruchomieniowa]]
- [[#Scaling plan]]
  - [[#Utrzymanie stałego poziomu]]
  - [[#Skalowanie manualne]]
  - [[#Skalowanie według harmonogramu]]
  - [[#Skalowanie dynamiczne]]
    - [[#Scaling Policy]]
    - [[#Scaling cooldown]]
    - [[#Skalowanie predykcyjne]]
- [[#Lifecycle hook]]
- [[#Auto Scaling Alarm]]
- [[#Auto Scaling dla ELB]]
- [[#See also]]

# Auto Scaling Group

[źródło](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html)

ASG zawiera zbiór [[EC2]], które są traktowane jako logiczne zgrupowanie dla celów automatycznego skalowania i zarządzania. ASG umożliwia również korzystanie z funkcji EC2 Auto Scaling, takich jak zastępowanie kontroli stanu i polityki skalowania. Zarówno utrzymywanie liczby instancji w ASG, jak i automatyczne skalowanie są podstawową funkcjonalnością usługi EC2 Auto Scaling.

Rozmiar ASG zależy od liczby[[EC2]], którą ustawisz jako pożądaną pojemność (**desired**). Możesz dopasować jej wielkość do zapotrzebowania - ręcznie lub za pomocą automatycznego skalowania.

ASG zaczyna od uruchomienia wystarczającej liczby [[EC2]], aby osiągnąć pożądaną pojemność. Utrzymuje tę liczbę, wykonując okresowe kontrole stanu (**heatlh check**) [[EC2]] w grupie. ASG kontynuuje utrzymywanie stałej liczby, nawet jeśli jakaś [[EC2]] stanie się niezdrowa. Grupa kończy niezdrową [[EC2]] i uruchamia inną, aby ją zastąpić.

ASG posiadają następujące atrybuty:

- [[#Szablon uruchomieniowy]],
  - [[AMI]] + typ [[EC2]],
  - dane użytkownika EC2,
  - wolumeny [[Elastic Block Store]],
  - [[Security group]],
  - para kluczy [[SSH]],
- rozmiar minimalny / rozmiar maksymalny / pojemność początkowa,
- informacje o [[VPC]] i [[VPC Subnet]],
- informacje o [[Elastic Load Balancing|ELB]],
- [[#Polityka skalowania]].

# Szablon uruchomieniowy

[źródło](https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchTemplates.html)

Szablon uruchomienia (**lauch template**) określa informacje o konfiguracji [[EC2]], którego ASG używa do uruchamiania instancji. Zawiera ID [[AMI]], typ instancji, parę kluczy, [[Security group]] i inne parametry używane do uruchamiania [[EC2]].

Definiowanie szablonu startowego pozwala na posiadanie wielu jego wersji. Dzięki wersjonowaniu szablonów uruchamiania można utworzyć podzbiór pełnego zestawu parametrów. Następnie można go ponownie wykorzystać do tworzenia innych wersji tego samego szablonu uruchamiania.

Aby zaktualizować ASG, należy dostarczyć nową wersję szablonu.

# Konfiguracja uruchomieniowa

`depprecated`

Zaleca się korzystanie z [[#Szablon uruchomieniowy|szablonów uruchomieniowych]] zamiast konfiguracji uruchomieniowych, aby mieć pewność, że można korzystać z najnowszych funkcji [[EC2]].

Np. nie można utworzyć ASG, która uruchamia zarówno Instancje Spot jak i On-Demand, ani ASG, która określa wiele typów instancji. Brakuje też opcji versjonowania szablonów.

# Scaling plan

[źródło](https://docs.aws.amazon.com/autoscaling/ec2/userguide/scaling_plan.html)

Dla EC2 Auto Scaling dostępne są następujące plany skalowania:

- [[#Utrzymanie stałego poziomu]],
- [[#Skalowanie manualne]],
- [[#Skalowanie według harmonogramu]],
- [[#Skalowanie dynamiczne]],
- [[#Skalowanie predykcyjne]].

## Utrzymanie stałego poziomu

ASG może utrzymywać określoną liczbę działających [[EC2]] przez cały czas. Aby utrzymać aktualny poziom instancji, EC2 Auto Scaling wykonuje okresowe sprawdzanie stanu uruchomionych instancji w ramach ASG. Gdy Auto Scaling znajdzie niezdrową instancję, kończy jej działanie i uruchamia nową.

## Skalowanie manualne

Skalowanie ręczne to najbardziej podstawowy sposób skalowania zasobów, w którym określasz tylko zmianę maksymalnej, minimalnej lub pożądanej pojemności ASG. Auto Scaling zarządza procesem tworzenia lub kończenia instancji, aby utrzymać zaktualizowaną pojemność.

## Skalowanie według harmonogramu

Skalowanie według harmonogramu oznacza, że akcje skalowania są wykonywane automatycznie w funkcji czasu i daty. Jest to przydatne, gdy dokładnie wiesz, kiedy zwiększyć lub zmniejszyć liczbę instancji w swojej grupie, po prostu dlatego, że potrzeba pojawia się w przewidywalnym harmonogramie.

## Skalowanie dynamiczne

Skalowanie dynamiczne pozwala zdefiniować [[#Scaling Policy]], która dynamicznie zmienia rozmiar ASG, aby sprostać zmianom w zapotrzebowaniu. Ta metoda jest przydatna do skalowania w odpowiedzi na zmieniające się warunki, gdy nie wiesz, kiedy te warunki się zmienią.

### Scaling Policy

Możesz użyć polityk skalowania (**scaling policy**), aby [[#Skalowanie dynamiczne|dynamicznie]] zmieniać liczbę [[EC2]] w swojej grupie.
ASG dostosowuje w razie potrzeby pożądaną pojemność grupy, zgonie zastosowaną polityką, pomiędzy minimalnymi i maksymalnymi wartościami pojemności.

Auto Scaling wspietra następujące typy dynamicznych polityk:

- skalowanie w oparciu o śledzenie celu (**target tracking scaling**) - zmiana aktualnej pojemności ASG w oparciu o wartość docelową dla określonej metryki;

- skalowanie krokowe (**step scaling**) - zmiana aktualnej pojemności ASG w oparciu o zestaw dopasowań skalowania, zwanych dopasowaniami krokowymi (**step adjustment**), które różnią się w zależności od wielkości naruszenia alarmu;

- skalowanie proste (**simple scaling**) - zmiana aktualnej pojemności AGS na podstawie pojedynczej regulacji skalowania.

### Scaling cooldown

Cooldown skalowania pomaga uniknąć sytuacji, w której Auto Scaling uruchamia lub kończy dodatkowe instancje podczas **skalowania prostego** zanim efekty poprzednich działań będą widoczne. Odpowiedni okres cooldown pomaga zapobiec zainicjowaniu dodatkowej aktywności skalowania w oparciu o nieaktualne metryki. Dymyślnie 300s.

> W większości przypadków polityka skalowania ze śledzeniem (**target tracking scaling**) celu lub polityka skalowania krokowego (**step scaling**) jest bardziej optymalna dla wydajności skalowania niż czekanie na upłynięcie ustalonego okresu czasu po wystąpieniu czynności skalowania. Zwłaszcza w przypadku polityki skalowania, która zmienia rozmiar ASG proporcjonalnie w miarę zmniejszania lub zwiększania wartości metryki skalowania.

## Skalowanie predykcyjne

Możesz także połączyć skalowanie predykcyjne i dynamiczne (odpowiednio podejście proaktywne i reaktywne), aby szybciej skalować [[EC2]].


# Lifecycle hook

[źródło](https://docs.aws.amazon.com/autoscaling/ec2/userguide/lifecycle-hooks.html#lifecycle-hooks-overview)

EC2 Auto Scaling oferuje możliwość dodawania haków cyklu życia do swoich ASG. Te haki pozwalają tworzyć rozwiązania, które są świadome zdarzeń w cyklu życia instancji Auto Scaling, a następnie wykonać niestandardową akcję na instancjach, gdy wystąpi odpowiednie zdarzenie cyklu życia. Hak cyklu życia zapewnia określony czas (domyślnie jedna godzina) oczekiwania na zakończenie akcji, zanim instancja przejdzie do następnego stanu.

![[EC2 AS hook.png]]

Popularnym zastosowaniem haków lifecycle jest kontrola, kiedy instancje są rejestrowane w [[Elastic Load Balancing]]. Dodając **launch lifecycle hook** do ASG, możesz upewnić się, że skrypty startowe zakończyły się sukcesem, a aplikacje na instancjach są gotowe do przyjęcia ruchu, zanim zostaną zarejestrowane do load balancera na końcu lifecycle hook.

# Auto Scaling Alarm

Możliwe jest skalowanie na podstawie alarmach [[CloudWatch]]. Metryki, które można użyć w tym celu to m.in:

- wykożystanie CPU, RAM
- liczba zapytań,
- uśredniony ruch it/out,
- własne metryki.

# Auto Scaling dla ELB

Jeśli rejestrujesz cele [[Elastic Load Balancing]] według ID [[EC2]], możesz użyć load balancera z ASG. Po dołączeniu [[Elastic Load Balancing#Target group|target grupy]] do ASG, Auto Scaling rejestruje swoje cele z [[Elastic Load Balancing#Target group|Target group]] dla Ciebie, gdy je uruchamia, a [[EC2]], które są kończone przez Auto Scaling są automatycznie wyrejestrowywane z load balancera.

---
# See also

- [[Auto Scaling]]
- [[Elastic Load Balancing]]