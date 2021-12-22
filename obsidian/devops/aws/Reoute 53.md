#AWS #network

Amazon Route 53 to wysoce dostępna i skalowalna usługa sieciowa systemu nazw domen ([[DNS]]) w chmurze. Route 53 jest również w pełni zgodny z IPv6.

Route 53 łączy żądania użytkowników do infrastruktury działającej w AWS,
takiej jak:

- instancje [[EC2]],
- balansery obciążenia [[Elastic Load Balancing]]
- kubełki (_bucket_) Amazon [[S3]]

Może być również używany do kierowania użytkowników do infrastruktury poza AWS.
Możesz wykorzystać Amazon Route 53 do skonfigurowania kontroli stanu DNS, a następnie stale monitorować zdolność aplikacji do odzyskiwania danych po awariach i kontrolować odzyskiwanie aplikacji za pomocą Route 53 [[# Application Recovery Controller]].

# Application Recovery Controller

#TODO

# Inne usłygi Route 53

#TODO

Amazon Route 53 Traffic Flow ułatwia globalne zarządzanie ruchem poprzez różne rodzaje routingu, w tym [[# Latency Based Routing]], [[# Geo DNS]], [[# Geoproximity]] i [[# Weighted Round Robin]] - wszystkie te funkcje można połączyć z [[# DNS Failover]], aby umożliwić tworzenie różnych architektur o niskich opóźnieniach i odpornych na awarie. Korzystając z prostego edytora wizualnego Amazon Route 53 Traffic Flow, można łatwo zarządzać sposobem, w jaki użytkownicy końcowi są kierowani do punktów końcowych aplikacji - niezależnie od tego, czy znajdują się one w jednym regionie AWS, czy są rozproszone po całym świecie. Amazon Route 53 oferuje również rejestrację nazw domen - możesz zakupić i zarządzać nazwami domen, takimi jak example.com, a Amazon Route 53 automatycznie skonfiguruje ustawienia DNS dla Twoich domen.
