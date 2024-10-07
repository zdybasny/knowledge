#todo

[źródło](https://www.pulumi.com/docs/iac/concepts/resources/components/)

Zasób składowy (dalej komponent) tworzy instancję zestawu powiązanych zasobów. A agreguje je w kontruktorze jako elementy potomne i tworzy abstrakcję, która hermetyzuje szczegóły ich implementacji.

Np. `Vpc`, który ma zaimplementowane tworzenie zasobów zgodnie z dobrymi praktykami.

# Implementacja komponentu

> Na implementację pełnej kontroli cyklu życia własnego zasobu zezwala [dostawca dynamiczny](Dynamic provider).

## Kontruktor

Aby stworzyć komponent, należy:
* dziedziczyć po klasie `pulumi.ComponentResource`,
  * przekazując do niego argumenty typu `name`, `MyComponentArgs` i `pulumi.ComponentResourceOptions` oraz tw
* stworzyć zasoby potomne w kontruktorze, 
  * przekazując im rodzica w `pulumi.ComponentResourceOptions`, aby je poprawnie sparować.

Zasoby stworzone jako część zasobu składowego (component resource) powinny zapewnić swoje nazwy unikalne pomiędzy innymi komponentowymi. Do tego celu powinny zostać użyte nazwy komponentów.

```TypeScript
class MyComponent extends pulumi.ComponentResource {
    constructor(name: string, myComponentArgs: MyComponentArgs, opts: pulumi.ComponentResourceOptions) {
        super("pkg:index:MyComponent", name, {}, opts);
        let bucket = new aws.s3.Bucket(
            `${name}-bucket`,
            {/*...*/},
            {parent: this}
        );
    }
}
```

## Outputs

Zasoby komponentu mogą definiować własne właściwości wyjściowe za pomocą `registerOutputs`. Pulumi wyświetla je oraz ich zmiany podczas aktualizacji.

```TypeScript
this.registerOutputs({
    bucketDnsName: bucket.bucketDomainName,
})
```

> **Good practices**  
> Wywołanie `registerOutputs` zwykle ma miejsce na samym końcu konstruktora zasobu komponentu.  
>
> Kończy on rejestrowanie zasobów potomnych, więc komponent jest uważany za w pełni skonstruowany. Dlatego `registerOutputs` powinien być wywołany we wszystkich komponentach, nawet jeśli nie trzeba rejestrować żadnych wyjść.

## Dziedziczenie Resource Providers

#todo 