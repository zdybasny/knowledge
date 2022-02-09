#AWS #management #autoscaling #compute

EC2 Auto Scaling jest częścią usługi [Auto Scaling](Auto%20Scaling.md), która zapewnia uruchomienie pożądanej liczby [EC2](EC2.md), nawet jeśli jedna z nich ulegnie awarii, oraz umożliwia automatyczne zwiększanie lub zmniejszanie liczby instancji w miarę zmian zapotrzebowania na nie.

##### Spis treści

- [Auto Scaling Group](#Auto%20Scaling%20Group)
- [Szablon uruchomieniowy](#Szablon%20uruchomieniowy)
- [Konfiguracja uruchomieniowa](#Konfiguracja%20uruchomieniowa)
- [Scaling plan](#Scaling%20plan)
  - [Utrzymanie stałego poziomu](#Utrzymanie%20stałego%20poziomu)
  - [Skalowanie manualne](#Skalowanie%20manualne)
  - [Skalowanie według harmonogramu](#Skalowanie%20według%20harmonogramu)
  - [Skalowanie dynamiczne](#Skalowanie%20dynamiczne)
    - [Scaling Policy](#Scaling%20Policy)
    - [Scaling cooldown](#Scaling%20cooldown)
    - [Skalowanie predykcyjne](#Skalowanie%20predykcyjne)
- [Lifecycle hook](#Lifecycle%20hook)
- [Auto Scaling Alarm](#Auto%20Scaling%20Alarm)
- [Auto Scaling dla ELB](#Auto%20Scaling%20dla%20ELB)
- [See also](#See%20also)

# Auto Scaling Group

[źródło](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html)

ASG zawiera zbiór [EC2](EC2.md), które są traktowane jako logiczne zgrupowanie dla celów automatycznego skalowania i zarządzania. ASG umożliwia również korzystanie z funkcji EC2 Auto Scaling, takich jak zastępowanie kontroli stanu i polityki skalowania. Zarówno utrzymywanie liczby instancji w ASG, jak i automatyczne skalowanie są podstawową funkcjonalnością usługi EC2 Auto Scaling.

Rozmiar ASG zależy od liczby [EC2](EC2.md), którą ustawisz jako pożądaną pojemność (**desired**). Możesz dopasować jej wielkość do zapotrzebowania - ręcznie lub za pomocą automatycznego skalowania.

ASG zaczyna od uruchomienia wystarczającej liczby [EC2](EC2.md), aby osiągnąć pożądaną pojemność. Utrzymuje tę liczbę, wykonując okresowe kontrole stanu (**heatlh check**) [EC2](EC2.md) w grupie. ASG kontynuuje utrzymywanie stałej liczby, nawet jeśli jakaś [EC2](EC2.md) stanie się niezdrowa. Grupa kończy niezdrową [EC2](EC2.md) i uruchamia inną, aby ją zastąpić.

ASG posiadają następujące atrybuty:

- [Szablon uruchomieniowy](#Szablon%20uruchomieniowy),
  - [AMI](AMI.md) + typ [EC2](EC2.md),
  - dane użytkownika EC2,
  - wolumeny [Elastic Block Store](Elastic%20Block%20Store.md),
  - [Security group](Security%20group.md),
  - para kluczy [SSH](../network/SSH.md),
- rozmiar minimalny / rozmiar maksymalny / pojemność początkowa,
- informacje o [VPC](VPC.md) i [VPC Subnet](VPC%20Subnet.md),
- informacje o [ELB](Elastic%20Load%20Balancing.md),
- [Polityka skalowania](#Polityka%20skalowania).

# Szablon uruchomieniowy

[źródło](https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchTemplates.html)

Szablon uruchomienia (**lauch template**) określa informacje o konfiguracji [EC2](EC2.md), którego ASG używa do uruchamiania instancji. Zawiera ID [AMI](AMI.md), typ instancji, parę kluczy, [Security group](Security%20group.md) i inne parametry używane do uruchamiania [EC2](EC2.md).

Definiowanie szablonu startowego pozwala na posiadanie wielu jego wersji. Dzięki wersjonowaniu szablonów uruchamiania można utworzyć podzbiór pełnego zestawu parametrów. Następnie można go ponownie wykorzystać do tworzenia innych wersji tego samego szablonu uruchamiania.

Aby zaktualizować ASG, należy dostarczyć nową wersję szablonu.

# Konfiguracja uruchomieniowa

`depprecated`

Zaleca się korzystanie z [szablonów uruchomieniowych](#Szablon%20uruchomieniowy) zamiast konfiguracji uruchomieniowych, aby mieć pewność, że można korzystać z najnowszych funkcji [EC2](EC2.md).

Np. nie można utworzyć ASG, która uruchamia zarówno Instancje Spot jak i On-Demand, ani ASG, która określa wiele typów instancji. Brakuje też opcji versjonowania szablonów.

# Scaling plan

[źródło](https://docs.aws.amazon.com/autoscaling/ec2/userguide/scaling_plan.html)

Dla EC2 Auto Scaling dostępne są następujące plany skalowania:

- [Utrzymanie stałego poziomu](#Utrzymanie%20stałego%20poziomu),
- [Skalowanie manualne](#Skalowanie%20manualne),
- [Skalowanie według harmonogramu](#Skalowanie%20według%20harmonogramu),
- [Skalowanie dynamiczne](#Skalowanie%20dynamiczne),
- [Skalowanie predykcyjne](#Skalowanie%20predykcyjne).

## Utrzymanie stałego poziomu

ASG może utrzymywać określoną liczbę działających [EC2](EC2.md) przez cały czas. Aby utrzymać aktualny poziom instancji, EC2 Auto Scaling wykonuje okresowe sprawdzanie stanu uruchomionych instancji w ramach ASG. Gdy Auto Scaling znajdzie niezdrową instancję, kończy jej działanie i uruchamia nową.

## Skalowanie manualne

Skalowanie ręczne to najbardziej podstawowy sposób skalowania zasobów, w którym określasz tylko zmianę maksymalnej, minimalnej lub pożądanej pojemności ASG. Auto Scaling zarządza procesem tworzenia lub kończenia instancji, aby utrzymać zaktualizowaną pojemność.

## Skalowanie według harmonogramu

Skalowanie według harmonogramu oznacza, że akcje skalowania są wykonywane automatycznie w funkcji czasu i daty. Jest to przydatne, gdy dokładnie wiesz, kiedy zwiększyć lub zmniejszyć liczbę instancji w swojej grupie, po prostu dlatego, że potrzeba pojawia się w przewidywalnym harmonogramie.

## Skalowanie dynamiczne

Skalowanie dynamiczne pozwala zdefiniować [Scaling Policy](#Scaling%20Policy), która dynamicznie zmienia rozmiar ASG, aby sprostać zmianom w zapotrzebowaniu. Ta metoda jest przydatna do skalowania w odpowiedzi na zmieniające się warunki, gdy nie wiesz, kiedy te warunki się zmienią.

### Scaling Policy

Możesz użyć polityk skalowania (**scaling policy**), aby [dynamicznie](#Skalowanie%20dynamiczne) zmieniać liczbę [EC2](EC2.md) w swojej grupie.
ASG dostosowuje w razie potrzeby pożądaną pojemność grupy, zgonie zastosowaną polityką, pomiędzy minimalnymi i maksymalnymi wartościami pojemności.

Auto Scaling wspietra następujące typy dynamicznych polityk:

- skalowanie w oparciu o śledzenie celu (**target tracking scaling**) - zmiana aktualnej pojemności ASG w oparciu o wartość docelową dla określonej metryki;

- skalowanie krokowe (**step scaling**) - zmiana aktualnej pojemności ASG w oparciu o zestaw dopasowań skalowania, zwanych dopasowaniami krokowymi (**step adjustment**), które różnią się w zależności od wielkości naruszenia [alarmu](CloudWatch.md#Alarm);

- skalowanie proste (**simple scaling**) - zmiana aktualnej pojemności AGS na podstawie pojedynczej regulacji skalowania.

### Scaling cooldown

Cooldown skalowania pomaga uniknąć sytuacji, w której Auto Scaling uruchamia lub kończy dodatkowe instancje podczas **skalowania prostego** zanim efekty poprzednich działań będą widoczne. Odpowiedni okres cooldown pomaga zapobiec zainicjowaniu dodatkowej aktywności skalowania w oparciu o nieaktualne metryki. Dymyślnie 300s.

> W większości przypadków polityka skalowania ze śledzeniem (**target tracking scaling**) celu lub polityka skalowania krokowego (**step scaling**) jest bardziej optymalna dla wydajności skalowania niż czekanie na upłynięcie ustalonego okresu czasu po wystąpieniu czynności skalowania. Zwłaszcza w przypadku polityki skalowania, która zmienia rozmiar ASG proporcjonalnie w miarę zmniejszania lub zwiększania wartości metryki skalowania.

## Skalowanie predykcyjne

Możesz także połączyć skalowanie predykcyjne i dynamiczne (odpowiednio podejście proaktywne i reaktywne), aby szybciej skalować [EC2](EC2.md).

# Lifecycle hook

[źródło](https://docs.aws.amazon.com/autoscaling/ec2/userguide/lifecycle-hooks.html#lifecycle-hooks-overview)

EC2 Auto Scaling oferuje możliwość dodawania haków cyklu życia do swoich ASG. Te haki pozwalają tworzyć rozwiązania, które są świadome zdarzeń w cyklu życia instancji Auto Scaling, a następnie wykonać niestandardową akcję na instancjach, gdy wystąpi odpowiednie zdarzenie cyklu życia. Hak cyklu życia zapewnia określony czas (domyślnie jedna godzina) oczekiwania na zakończenie akcji, zanim instancja przejdzie do następnego stanu.

![](attachments/EC2%20AS%20hook.png)

Popularnym zastosowaniem haków lifecycle jest kontrola, kiedy instancje są rejestrowane w [Elastic Load Balancing](Elastic%20Load%20Balancing.md). Dodając **launch lifecycle hook** do ASG, możesz upewnić się, że skrypty startowe zakończyły się sukcesem, a aplikacje na instancjach są gotowe do przyjęcia ruchu, zanim zostaną zarejestrowane do load balancera na końcu lifecycle hook.

# Auto Scaling Alarm

Możliwe jest skalowanie na podstawie [alarmów CloudWatch](CloudWatch.md#Alarm). Metryki, które można użyć w tym celu to m.in:

- wykożystanie CPU, RAM
- liczba zapytań,
- uśredniony ruch it/out,
- własne metryki.

# Auto Scaling dla ELB

Jeśli rejestrujesz cele [Elastic Load Balancing](Elastic%20Load%20Balancing.md) według ID [EC2](EC2.md), możesz użyć load balancera z ASG. Po dołączeniu [target grupy](Elastic%20Load%20Balancing.md#Target%20group) do ASG, Auto Scaling rejestruje swoje cele z [Target group](Elastic%20Load%20Balancing.md#Target%20group) dla Ciebie, gdy je uruchamia, a [EC2](EC2.md), które są kończone przez Auto Scaling są automatycznie wyrejestrowywane z load balancera.

---

# See also

- [Auto Scaling](Auto%20Scaling.md)
- [Application Auto Scaling](Application%20Auto%20Scaling.md)
- [Elastic Load Balancing](Elastic%20Load%20Balancing.md)
- [EC2](EC2.md)
