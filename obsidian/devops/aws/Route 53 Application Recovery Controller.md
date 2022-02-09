#AWS #network

[źródło](https://docs.aws.amazon.com/r53recovery/latest/dg/what-is-route53-recovery.html)

**Amazon Route 53 Application Recovery Controller** daje wgląd w to, czy aplikacje i zasoby są gotowe do odzyskania, a także pomaga zarządzać i koordynować [przełączanie awaryjne DNS](Route%2053.md#Failover).

Udostępnia dwie odrębne, ale uzupełniające się funkcje:

- **Routing Control**,
- **Readiness Check**.

Funkcje te można wykorzystać razem, aby uzyskać wgląd w to, czy aplikacje i zasoby są gotowe do odzyskiwania, a także aby pomóc w zarządzaniu i koordynowaniu przełączania awaryjnego. [Heatlh Checki Route 53](Route%2053.md#Health%20Check) w **Application Recovery Controller** są powiązane z [Routing Control](#Routing%20Control), które są prostymi przełącznikami typu włącz/wyłącz.

##### Spis treści

- [Routing Control](#Routing%20Control)
- [Readiness Check](#Readiness%20Check)
- [See also](#See%20also)

# Routing Control

[źródło](https://docs.aws.amazon.com/r53recovery/latest/dg/routing-control.html)

**Routing Controls** używa się, aby awaryjnie przekierować ruch (**failover**) do replik aplikacji (np. pomiędzy [AZ](AWS%20locations.md#Availability%20zone) lub [Regionami](AWS%20locations.md#Region) AWS) w **Application Recovery Controller**.

. Są one

**Routing Controls** to proste przełączniki typu włącz/wyłącz, zgrupowane w panelu kontrolnym i hostowane na klastrze w **Application Recovery Controller**, które kontrolują przekierowywanie ruchu klienckiego, z użyciem [kontroli stanu w Route 53](Route%2053.md#Weighted).

Struktury **kontroli routingu** w Application Recovery o:

- klaster,
- panel sterowania (opcjonalny),
- kontrole routingu,
- kontrole stanu kontroli routingu ([Health Check](Route%2053.md#Health%20Check))

# Readiness Check

[źródło](https://docs.aws.amazon.com/r53recovery/latest/dg/recovery-readiness.html)

**Sprawdzanie gotowości** umożliwia **Application Recovery Controller** ciągłe sprawdzanie gotowości zasobów aplikacji,
Może monitorować i zarządzać gotowością do odzyskiwania.

Można również skonfigurować funkcję autoryzacji między kontami w **Application Recovery Controller**, aby ułatwić konfigurowanie i monitorowanie rozproszonych zasobów z jednego konta AWS.

---

# See also

- [Route 53](Route%2053.md)
