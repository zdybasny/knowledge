#AWS #network

**Elastyczny interfejs sieciowy** jest logicznym komponentem sieciowym w [[VPC]], który reprezentuje wirtualną kartę sieciową. Możesz tworzyć i konfigurować interfejsy sieciowe oraz dołączać, odłączać i przepinać je do [[EC2]] w tej samej [[AWS locations#Availability zone|Availability zone]].

Każda [[EC2]] ma domyślny interfejs sieciowy, zwany podstawowym interfejsem sieciowym. Nie możesz odłączyć podstawowego interfejsu sieciowego od instancji. Możesz tworzyć i dołączać dodatkowe interfejsy sieciowe. Maksymalna liczba interfejsów sieciowych, których możesz używać, różni się w zależności od typu instancji.

Twoje konto może również posiadać interfejsy sieciowe zarządzane przez requestera (_requester-managed_), które są tworzone i zarządzane przez usługi AWS, aby umożliwić Ci korzystanie z innych zasobów i usług. Nie możesz samodzielnie zarządzać tymi interfejsami sieciowymi.
