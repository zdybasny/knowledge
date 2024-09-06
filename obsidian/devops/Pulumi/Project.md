[źródło](https://www.pulumi.com/docs/concepts/projects/project-file/)

Projekt Pulumi to dowolny folder zawierający plik projektu `Pulumi.yaml`. Projekty są tworzone za pomocą polecenia `pulumi new`.

# Pulumi.yaml

Plik ten określa metadane dotyczące projektu, takie jak nazwa projektu `name:` czy środo uruchomieniowe wybranego języka `runtime:`. 

Funkcja `getProject()` zwraca nazwę aktualnie wdrażanego projektu. Może to być przydatne do nazywania lub oznaczania zasobów.

## Atrybuty

Dostępne atrybuty:

* `name` [wymagany] - Nazwa projektu zawierająca znaki `[a-zA-Z0-9]`, `-`, `_`, `.`.
* `runtime` [wymagany] - Wspierane języki to `nodejs`, `python`, `go`, `dotnet`, `java` or `yaml`. W przypadku definiowania infrastruktury w YAML wystarczy samo [Pulumi CLI](Pulumi%20CLI).
* `main` - Wskazuje na folder do programu, jeśli jest inny niż folder z plikiem `Pulumi.yaml`
* `description`
* `stackConfigDir`
* `backend`
* `options`
* `template`
* `plugins`


---
# See also

- https://www.pulumi.com/docs/languages-sdks/yaml/yaml-language-reference/
- 