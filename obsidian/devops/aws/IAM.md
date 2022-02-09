#AWS #security

[źródło](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)

**AWS Identity and Access Management** (IAM) umożliwia bezpieczne zarządzanie dostępem do usług i zasobów AWS. Korzystając z IAM, można tworzyć i zarządzać [użytkownikami](#IAM%20User) i [IAM User Group](#IAM%20User%20Group) AWS oraz używać [uprawnień](#Permission%20policies), aby zezwalać i odmawiać im dostępu do zasobów AWS.

IAM jest funkcją konta AWS oferowaną bez dodatkowych opłat. Zostaniesz obciążony jedynie za korzystanie z innych usług AWS przez Twoich użytkowników.

![](attachments/IAM%20components.png)

#### Spis treści

- [IAM User](#IAM%20User)
  - [Sfederowani użytkownicy](#Sfederowani%20użytkownicy)
- [IAM User Group](#IAM%20User%20Group)
- [IAM Role](#IAM%20Role)
  - [Service-linked role](#Service-linked%20role)
- [Permission policies](#Permission%20policies)
  - [Principal](#Principal)
  - [Action](#Action)
  - [Resource](#Resource)
  - [Effect](#Effect)
  - [Condition](#Condition)
- [IAM Security Tools](#IAM%20Security%20Tools)
- [IAM Credentials Report](#IAM%20Credentials%20Report)
  - [IAM Access Advisor](#IAM%20Access%20Advisor)
- [Best practices](#Best%20practices)
- [See also](#See%20also)

# IAM User

Użytkownik IAM reprezentuje osobę lub usługę, która używa użytkownika IAM do interakcji z AWS.
Podstawowym zastosowaniem użytkowników IAM jest umożliwienie ludziom zalogowania się do konsoli zarządzania AWS w celu wykonywania interaktywnych zadań oraz wykonywania programowych żądań do usług AWS za pomocą API lub CLI.

Użytkownik w AWS składa się z:

- nazwy,
- hasła do zalogowania się do konsoli zarządzania AWS
- maksymalnie dwóch kluczy dostępu, które mogą być używane z API lub CLI.

Kiedy tworzysz użytkownika IAM, nadajesz mu uprawnienia poprzez uczynienie go członkiem grupy użytkowników, która ma dołączone odpowiednie polityki uprawnień (zalecane), lub poprzez bezpośrednie dołączenie polityk do użytkownika.

## Sfederowani użytkownicy

Federacja tożsamości to system zaufania pomiędzy dwoma stronami w celu uwierzytelniania użytkowników i przekazywania informacji potrzebnych do autoryzacji ich dostępu do zasobów.

Możesz włączyć federację tożsamości, aby umożliwić istniejącym tożsamościom (użytkownikom, grupom i rolom) w przedsiębiorstwie dostępy bez konieczności tworzenia użytkownika IAM dla każdej tożsamości.

# IAM User Group

Grupa IAM jest zbiorem użytkowników IAM. Możesz użyć grup użytkowników, aby określić uprawnienia dla zbioru użytkowników, co może ułatwić zarządzanie tymi uprawnieniami dla tych użytkowników.

# IAM Role

Role IAM pozwalają na delegowanie dostępu dla użytkowników lub usług, które normalnie nie mają dostępu do zasobów AWS Twojej organizacji. Użytkownicy IAM lub usługi AWS mogą przyjąć rolę, aby uzyskać tymczasowe poświadczenia bezpieczeństwa, które mogą być używane do wywołań AWS API. Dzięki temu nie trzeba dzielić się długoterminowymi poświadczeniami lub definiować uprawnień dla każdego podmiotu, który wymaga dostępu do zasobu.

## Service-linked role

**Rola powiązana z usługą** to unikalny typ roli IAM, która jest powiązana bezpośrednio z usługą AWS. Takie role są predefiniowane przez usługę i obejmują wszystkie uprawnienia, których usługa wymaga do wywoływania innych usług AWS w imieniu użytkownika.

Powiązana usługa określa również sposób tworzenia, modyfikowania i usuwania roli powiązanej z usługą. Usługa może automatycznie tworzyć lub usuwać rolę. Niezależnie od metody, role powiązane z usługą ułatwiają konfigurację usługi, ponieważ nie musisz ręcznie dodawać niezbędnych uprawnień dla usługi, aby wykonać działania w Twoim imieniu.

# Permission policies

Uprawnienia pozwalają określić dostęp do zasobów AWS. Uprawnienia nadawane są podmiotom IAM (użytkownikom, grupom i rolom) i domyślnie podmioty te rozpoczynają pracę bez żadnych uprawnień.

Aby nadać podmiotom uprawnienia, możesz dołączyć politykę, która określa:

- Sid (opcjonalnie) - identyfikator
- [Principal](#Principal) - konto, użytkownik lub rola, której których dotyczy policy
- [Action](#Action) - lista akcji dozwolonych lub niedozwolonych
- [Resource](#Resource) - lista zasobów, których dotyczy policy
- [Effect](#Effect) - "Allow" / "Deny"
- [Condition](#Condition) (opjonalnie) - warunek wykonania policy

![](attachments/IAM%20Policy%20structure.png)

Zestaw dostępnych **akcji**, **zasobów** i **warunków** dla danego serwisu znajduje się w [Actions, resources, and condition keys for AWS services - Service Authorization Reference (amazon.com)](https://docs.aws.amazon.com/service-authorization/latest/reference/reference_policies_actions-resources-contextkeys.html)

## Principal

## Action

Które akcje usług AWS dopuszczasz. Wszelkie akcje, na które nie zezwolisz są odrzucane.

Na przykład, możesz zezwolić użytkownikowi na wywołanie akcji Amazon S3 ListBucket.

#ENG
After your request has been authenticated and authorized, AWS approves the actions or operations in your request. Operations are defined by a service, and include things that you can do to a resource, such as viewing, creating, editing, and deleting that resource.

## Resource

Na które zasoby AWS zezwalasz na akcję. Użytkownicy nie mają dostępu do żadnych zasobów, do których nie przyznasz im jawnie uprawnień.

Na przykład, możesz określić, na które kubełki Amazon S3 pozwolisz użytkownikowi wykonać akcję ListBucket.

## Effect

[źródło](https://docs.aws.amazon.com/IAM/latest/UserGuide/intro-structure.html#intro-structure-authorization)

Czy zezwolić lub odmówić dostępu. Ponieważ dostęp jest domyślnie odrzucany, zazwyczaj piszemy polityki, w których efektem jest zezwolenie.

#ENG
AWS checks each policy that applies to the context of your request. If a single permissions policy includes a denied action, AWS denies the entire request and stops evaluating. This is called an _explicit deny_. Because requests are _denied by default_, AWS authorizes your request only if every part of your request is allowed by the applicable permissions policies. The evaluation logic for a request within a single account follows these general rules:

- By default, all requests are denied. (In general, requests made using the AWS account root user credentials for resources in the account are always allowed.)

- An explicit allow in any permissions policy (identity-based or resource-based) overrides this default.

- The existence of an [Organizations SCP](Organizations%20SCP.md), **IAM permissions boundary**, or a session policy overrides the allow. If one or more of these policy types exists, they must all allow the request. Otherwise, it is implicitly denied.

- An explicit deny in any policy overrides any allows.

## Condition

Jakie warunki muszą być spełnione, aby polityka zaczęła obowiązywać.

Na przykład, możesz zezwolić na dostęp do określonych kubełków S3, jeśli użytkownik łączy się z określonego zakresu IP lub użył wieloczynnikowego uwierzytelniania przy logowaniu.

# IAM Security Tools

#ENG

# IAM Credentials Report

(account-level)
• a report that lists all your account's users and the status of their various
credentials

## IAM Access Advisor

(user-level)
• Access advisor shows the service permissions granted to a user and when those
services were last accessed.
• You can use this information to revise your policies.

# Best practices

Oto zalecenia Amazon dotyczące usługi AWS Identity and Access Management (IAM):

• Zablokuj klucze dostępu użytkownika root do konta AWS.
• Utwórz indywidualnych użytkowników IAM.
• Użyj grup użytkowników, aby przypisać uprawnienia do użytkowników IAM.
• Przyznaj najmniejsze uprawnienia.
• Zacznij używać uprawnień z politykami zarządzanymi przez AWS.
• Waliduj swoje polityki.
• Używaj polityk zarządzanych przez klienta zamiast polityk inline.
• Wykorzystaj poziomy dostępu do przeglądu uprawnień IAM.
• Skonfiguruj politykę silnych haseł dla swoich użytkowników.
• Włącz MFA.
• Użyj ról dla aplikacji, które działają na instancjach Amazon EC2.
• Użyj ról do delegowania uprawnień.
• Nie udostępniaj kluczy dostępu.
• Regularnie rotuj dane uwierzytelniające.
• Usuń niepotrzebne dane uwierzytelniające.
• Używaj warunków polityki dla dodatkowego bezpieczeństwa.
• Monitoruj aktywność na swoim koncie AWS.

Szczegółowe zalecenia znajdują się na stronie AWS: [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#lock-away-credentials).

---

# See also

- [Security best practices in IAM]((https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html.md#lock-away-credentials)
- [Actions, resources, and condition keys for AWS services - Service Authorization Reference (amazon.com)](https://docs.aws.amazon.com/service-authorization/latest/reference/reference_policies_actions-resources-contextkeys.html)
