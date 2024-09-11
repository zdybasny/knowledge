Stack jest odizolowaną, niezależną konfigurowalną instancją programu Pulumi.

A project can have as many stacks as you need. By default, Pulumi creates a stack for you when you start a new project using the pulumi new command.

Projekt może posiadać wiele stacków. Pulumi tworzy jeden podczas tworzenia projektu komendą `pulumi new`.

# outputs

Stos może eksportować wartości jako dane wyjściowe. Te dane wyjściowe są wyświetlane podczas aktualizacji, można je łatwo pobrać za pomocą Pulumi CLI i są wyświetlane w Pulumi Cloud. Mogą być używane do ważnych wartości, takich jak identyfikatory zasobów, obliczone adresy IP i nazwy DNS.


# tags

Metadane stacku w postaci par nazwy i wartości. Wbudowanych tagi są aktualizowane, gdy stos jest aktualizowany (np. `pulumi:project`, `gitHub:repo`).
Tagi są obsługiwane tylko przez Pulumi Cloud.
Tagi służą do grupowania stacków z wielu projektów, np. ze względu na środowisko (dev, staging, prod).

`pulumi stack tag ls` wyświetla tagi stosów.
`pulumi stack tag set <nazwa> <wartość>` przypisuje niestandardowy tag do stosu.
`pulumi stack tag rm <nazwa>` usuwa tag.
