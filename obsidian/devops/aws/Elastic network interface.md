#AWS #network #compute 

**Elastyczny interfejs sieciowy** jest logicznym komponentem sieciowym w [VPC](VPC.md), który reprezentuje wirtualną kartę sieciową. Możesz tworzyć i konfigurować interfejsy sieciowe oraz dołączać, odłączać i przepinać je do [EC2](EC2.md) w tej samej [Availability zone](AWS%20locations.md#Availability%20zone).

Każda [EC2](EC2.md) ma domyślny interfejs sieciowy, zwany podstawowym interfejsem sieciowym. Nie możesz odłączyć podstawowego interfejsu sieciowego od instancji. Możesz tworzyć i dołączać dodatkowe interfejsy sieciowe. Maksymalna liczba interfejsów sieciowych, których możesz używać, różni się w zależności od typu instancji.

Twoje konto może również posiadać interfejsy sieciowe zarządzane przez requestera (_requester-managed_), które są tworzone i zarządzane przez usługi AWS, aby umożliwić Ci korzystanie z innych zasobów i usług. Nie możesz samodzielnie zarządzać tymi interfejsami sieciowymi.
