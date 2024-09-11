[źródło](https://www.pulumi.com/docs/concepts/stack/)

Stack jest odizolowaną, niezależną konfigurowalną instancją programu Pulumi.

A project can have as many stacks as you need. By default, Pulumi creates a stack for you when you start a new project using the pulumi new command.

Projekt może posiadać wiele stacków. Pulumi tworzy jeden podczas tworzenia projektu komendą `pulumi new`.

Stack można pobrać programistycznie poprzez:

```TypeScript
let stack = pulumi.getStack();
```

# tags

Metadane stacku w postaci par nazwy i wartości. Wbudowanych tagi są aktualizowane, gdy stos jest aktualizowany (np. `pulumi:project`, `gitHub:repo`).
Tagi są obsługiwane tylko przez Pulumi Cloud.
Tagi służą do grupowania stacków z wielu projektów, np. ze względu na środowisko (dev, staging, prod).

`pulumi stack tag ls` wyświetla tagi stosów.
`pulumi stack tag set <nazwa> <wartość>` przypisuje niestandardowy tag do stosu.
`pulumi stack tag rm <nazwa>` usuwa tag.


# Stack outputs

Stos może eksportować wartości jako dane wyjściowe. Te dane wyjściowe są wyświetlane podczas aktualizacji, można je łatwo pobrać za pomocą Pulumi CLI i są wyświetlane w Pulumi Cloud. Wartość jest serializowana do typu JSON.

Aby wystawić output z programu:

```TypeScript
export let url = resource.url;
```

Aby wyświetlić output przez CLI:

`pulumi stack output url` lub `pulumi stack output --json`

Pulumi szyfruje sekrety. Flaga `--show-secret` wyświetlić je zdeszyfrowane.

Program musi eksportować funkcję asynchroniczną, aby móc użyć operator `await` na jego outpucie.

```TypeScript
export = async () => {
    // ...
}
```

# StackReference

Stacki mogą uzystać dostęp do danych wyjściowych innego stacku. Słuzy do tego obiekt `StackReference`.

Przykład pozyskania `service` z konfiguracji Kubernetes:

```TypeScript
import * as k8s from "@pulumi/kubernetes";
import * as pulumi from "@pulumi/pulumi";

const env = pulumi.getStack();
const infra = new pulumi.StackReference(`mycompany/infra/${env}`);
const provider = new k8s.Provider("k8s", { kubeconfig: infra.getOutput("kubeConfig") });
const service = new k8s.core.v1.Service(..., { provider: provider });
```

`StackReference` wspiera 2 rodzaje pobierania outputu:

* `getOutput()` zrwaca obiekt `Output`, przydatny gdy obiekt ma posłuzyć jako `Input` do innego zasobu;
* `getOutputDetails()` zwraca obiekt `OutputDetails` zapewniający bezpośredni dostęp do jego wartości, przydatny do bezpośredniego przetwarzania danych (serializownaych do JSON).

# Usuwanie zasobów i stacków

`pulumi destroy`usuwa zasoby ze stacku. Używa aktualnej konfiguracji, a nie tej, która była zastosowana podczas wdrożenia.

`pulumi stack rm` usuwa pusty stack.

`pulumi stack rm --force` usuwa stack z powiązynymi zasobami.