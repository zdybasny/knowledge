#AWS #management #autoscaling #database

[źródło](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PIOPS.StorageTypes.html#USER_PIOPS.Autoscaling)

**RDS Storage Auto Scaling** stale monitoruje rzeczywiste zużycie pamięci masowej dla [RDS](RDS.md) i skaluje wertykalnie pojemność automatycznie, gdy rzeczywiste wykorzystanie zbliża się do dostarczonej pojemności pamięci masowej.

Nadaje się to idealnie do nieprzewidywalnych obciążeń.

Nie ma żadnych dodatkowych kosztów dla **RDS Storage Auto Scaling**. Płacisz tylko za zasoby [RDS](RDS.md) potrzebne do uruchomienia aplikacji.

##### Spis treści

- [Triggery skalowania](#Triggery%20skalowania)
- [Pojemności skalowania](#Pojemności%20skalowania)
- [Obsługiwane silniki RDS](#Obsługiwane%20silniki%20RDS)
- [See also](#See%20also)

# Triggery skalowania

[RDS](RDS.md) rozpoczyna modyfikację pamięci masowej dla [instancji DB](RDS.md#Instancja%20bazy%20danych) z włączoną funkcją autoskalowania, gdy wystąpią poniższe czynniki:

- wolne dostępne miejsce jest mniejsze niż 10% zaalokowanej przestrzeni dyskowej,
- stan ten trwa co najmniej 5 minut,
- od ostatniej modyfikacji pamięci masowej minęło (w zależności od tego, który z tych okresów jest dłuższy):
  - co najmniej 6 godzin
  - lub optymalizacja pamięci masowej została zakończona na instancji

# Pojemności skalowania

Dodatkowa pamięć masowa jest przyrostowa w zależności od tego, która z poniższych wartości jest większa:

- 5 GiB,
- 10% aktualnie przydzielonej pamięci masowej,
- prognoza przyrostu pamięci masowej na 7 godzin na podstawie zmiany metryki `FreeStorageSpace` w ciągu ostatniej godziny.

Próg maksymalnej pamięci masowej (**Maximum Storage Threshold**) jest limitem, który trzebaustawić dla autoskalowania instancji DB. Nie można ustawić maksymalnego progu pamięci masowej dla instancji z włączoną funkcją autoskalowania na wartość większą niż maksymalna przydzielona pamięć masowa.

# Obsługiwane silniki RDS

RDS Storage Auto Scaling obsługuje wszystkie silniki bazodanowe:

- MariaDB,
- MySQL,
- PostgreSQL,
- SQL Server,
- Oracle.

---

# See also

- [Auto Scaling](Auto%20Scaling.md)
- [RDS](RDS.md)
