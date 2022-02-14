#github #pipelines

#TODO

**Akcja** to niestandardowa aplikacja na platformie **GitHub Actions**, która wykonuje złożone, ale często powtarzane zadanie. Użyj **akcji**, aby zredukować ilość powtarzającego się kodu, który piszesz w swoich plikach przepływu pracy. Akcja może ściągnąć repozytorium git z GitHuba, skonfigurować poprawny toolchain dla Twojego środowiska budowania lub ustawić uwierzytelnianie do Twojego dostawcy chmury.

Możesz pisać własne akcje lub znaleźć je w GitHub Marketplace, aby użyć ich w swoich przepływach pracy.

# Custom action

Można używać custom actions pod pewnymi obostrzeniami w zależności od tego, na jakim repozytorium one się znajdują:

- `private` - tylko workflow na tym samym repozytorium co akcja,
- `internal` - tylko repozytoria w ramach organizacji
- `public` - bez ograniczeń.

# Expression
