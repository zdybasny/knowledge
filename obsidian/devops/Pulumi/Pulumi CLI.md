#todo

`pulumi new`

Pulumi tworzy jeden stack podczas tworzenia projektu.

`pulumi config`

`pulumi preview`

Wyświetla podgląd aktualizacji aktualnie wybranego stacku. 

`pulumi preview --save-plan=plan.json`

Zapisuje ten plan do pliku.

`pulumi up`

Aktualizuje aktualnie wybrany stack.

`pulumi up --plan=plan.json` ogranicza aktualizację tylko tego, co zostało zaplanowane w pliku planu.

`pulumi update`

`pulumi destroy`

`pulumi stack`

Wyświetla metadane, zasoby i propercje wyściowe (OUTPUT) aktywnego stacku.

`pulumi stack init <stack-name>`

Tworzy nowy pusty stack dla najbliższego pliku `Pulumi.yaml` i ustawia go jako aktywny. Tworzy przy plik `Pulumi.<stack-name>.yaml`.

`pulumi stack ls`

Wyświetla listę staków.

`pulumi stack select <stack-namw>`

`pulumi stack tag ls` wyświetla tagi stosów.
`pulumi stack tag set <nazwa> <wartość>` przypisuje niestandardowy tag do stosu.
`pulumi stack tag rm <nazwa>` usuwa tag.
