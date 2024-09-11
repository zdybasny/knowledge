Zasoby reprezentują podstawowe jednostki tworzące infrastrukturę.

Wszystkie zasoby infrastruktury są opisywane przez jedną z dwóch podklas klasy `Resource`:

* `CustomResource` - zasób zarządzany przez dostawcę zasobów (resource provider),
* `ComponentResource` - logiczne zgrupowanie innych zasobów, które hermetyzuje szczegóły jego implementacji.

Argumenty zasobu:

* `name` - unikalna nazwa w ramach jednego staku. Pulumi automatycznie tworzy nazwy dla zasobów fizycznych, co może być nadpisane w razie potrzeby.
* `args` - zestaw nazwanych wartości, które są używane do inicjalizacji zasobu. Mogą to być proste typy (takie jak `string`, `int`, `map`) lub `Output` z innych zasobów.
* `options` (opcjonalny) - pozwala kontrolować niektóre aspekty zasobu, np. jawne zależności, niestandardowa konfiguracji dostawcy.

