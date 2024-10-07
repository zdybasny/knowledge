Zasoby reprezentują podstawowe jednostki tworzące infrastrukturę.

Wszystkie zasoby infrastruktury są opisywane przez jedną z dwóch podklas klasy `Resource`:

* `CustomResource` - zasób zarządzany przez dostawcę zasobów (resource provider),
* `ComponentResource` - logiczne zgrupowanie innych zasobów, które hermetyzuje szczegóły jego implementacji.

Argumenty zasobu:

* `name` - unikalna nazwa logiczna w ramach jednego staku. Pulumi automatycznie tworzy nazwy dla zasobów fizycznych, co może być nadpisane w razie potrzeby.
* `args` - zestaw nazwanych wartości, które są używane do inicjalizacji zasobu. Mogą to być proste typy (takie jak `string`, `int`, `map`) lub `Output` z innych zasobów.
* `options` (opcjonalny) - pozwala kontrolować niektóre aspekty zasobu, np. jawne zależności, niestandardowa konfiguracji dostawcy.

Each resource is an instance of a specific Pulumi resource type. This type is specified by a type token in the format <package>:<module>:<typename>. Concrete examples of this format are:

Każdy zasób jest instancją konkretnego typu, który jest określany poprzez `<pakiet>:<moduł>:<typ>`,  np. `kubernetes:apps/v1:Deployment`, gdzie:
* `<pakiet>` okresla który Pulumi Package definuije zasób,
*  `<moduł>` to ścieżka, gdzie w pakiecie znajduje się zasób
* `<typ>` to typ zasobu.

# Nazwy fizyczne i automatyczne nadawanie nazw

Nawet jeśli zaasóv ma logiczną nazwę `my-role`, nazwa fizyczna będzie zazwyczaj wyglądać jak `my-role-d7c2fa0` z solowym sufiksem.
* Zapewnia, że dwa stosy dla tego samego projektu mogą zostać wdrożone bez kolizji ich zasobów. 
* Pozwala Pulumi na aktualizację zasobów bez przestojów. Niektóre zasoby usługi zastąpienia zasobów zamiast aktualizowania ich w miejscu. 
 > Domyślnie Pulumi najpierw tworzy zamienniki, następnie aktualizuje istniejące odniesienia do nich, a na koniec usuwa stare zasoby.

```TypeScript
let role = new aws.iam.Role("my-logical-role", {
    name: `my-physical-role-${pulumi.getProject()}-${pulumi.getStack()}`,
}, { deleteBeforeReplace: true });
```

> Niektóre zasoby używają innej właściwości niż `name` podania własnych nazw fizycznych, a inne nie mają fizycznych nazw i używają innych automatycznie generowanych identyfikatorów do ich jednoznacznej identyfikacji.

Zastąpienie automatycznego nazewnictwa sprawia, że projekt jest podatny na kolizje nazw. W rezultacie dla zasobów, które mogą wymagać zastąpienia, należy określić `deleteBeforeReplace: true`.

# URN zasobu

Kazdy zasób ma przypisane URN (Uniform Resource Name), który identyfikuje go globalnie.

```
urn:pulumi:production::acmecorp-website::custom:resources:Resource$aws:s3/bucket:Bucket::my-bucket
           ^^^^^^^^^^  ^^^^^^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^  ^^^^^^^^^
           <stack-name> <project-name>   <parent-type>             <resource-type>       <resource-name>
```

Każda zmiana URN - np. poprzez zmianę nazwy fizycznej - powoduje usunięcie starego zasobu i utworzenie nowego, jako że stają się niepowiązane.
 
