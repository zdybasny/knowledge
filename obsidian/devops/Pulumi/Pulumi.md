Pulumi wykorzystuje model pożądanego stanu (deklaratywny) do orkiestracji i zarządzania infrastrukturą przy użyciu języków programowania, takich jak TypeScript, JavaScript, Python, Go, C# i Java. Model ten zapewnia zalety języków wysokopoziomych oraz autouzupełnianie i typowaniem przez IDE. 

# Deployment engine

Silnik wdrażania (**deployment engine**) porównuje ten pożądany stan z bieżącym stanem stosu i określa, jakie zasoby należy utworzyć, zaktualizować lub usunąć. Silnik wykorzystuje zestaw dostawców zasobów ([Resource provider](Resource%20provider.md)) (takich jak AWS, Azure, Kubernetes) w celu zarządzania poszczególnymi zasobami. Podczas działania silnik aktualizuje stan infrastruktury o informacje o wszystkich zasobach, które zostały udostępnione, a także o wszelkich oczekujących operacjach.

Silnik wdrażania jest osadzony w samym interfejsie [Pulumi CLI](Pulumi%20CLI.md).

