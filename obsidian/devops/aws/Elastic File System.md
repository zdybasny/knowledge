#AWS #storage

[źródło]([What is Amazon Elastic File System? - Amazon Elastic File System](https://docs.aws.amazon.com/efs/latest/ug/whatisefs.html))

Elastic File System (EFS) to prosty, bezserwerowy, elastyczny system plików typu "set-and-forget" do użytku z usługami w chmurze AWS i zasobami lokalnymi. Jest to w pełni zarządzany system plików **NFS** (network file system), który został zaprojektowany specjalnie dla obciążeń Linux (bazuje na interfejsie [POSIX](https://pl.wikipedia.org/wiki/POSIX)) dla wielu [[EC2]] jednocześnie. Umożliwia również przechowywanie danych w wielu [[AWS locations#Availability zone|AZ]], dzięki czemu domyślnie uzyskuje się pewną trwałość. Obsługuje skalowanie do petabajtów danych.

Dostępna jest opcja szyfrowania w spoczynky używająca [[Key Management Service]].

EFS jest znacznie droższy od [[Elastic Block Store]], ale płaci się faktyczne użycie, do którego  EFS się skaluje.

##### Spis treści

- [[#Infrequent-Access]]
- [[#One Zone]]
- [[#Tryby EFS]]
- [[#See also]]

# Infrequent-Access

#TODO

Istnieje także możliwość redukcji kosztów poprzez automatyczne przenoszenia danych do tańszej warstwy nieczęstego dostępu (**Infrequent-Access**).

# One Zone

#TODO

Kolejną opcją na redukjce kosztów jest użycie **EFS One Zone** oraz **EFS One Zone-Infrequent Access**.

# Tryby EFS

EFS został zaprojektowany, aby zapewnić przepustowość, IOPS i niskie opóźnienia potrzebne do szerokiego zakresu obciążeń. Dostępe po 2 tryby wydajności i przepustowości:

- **Performance mode**:

  - **General Purpose** - domyślny - Iidealny dla przypadków użycia wrażliwych na opóźnienia, takich jak środowiska serwowania stron internetowych, systemy zarządzania treścią, katalogi domowe i ogólne serwowanie plików;

  - **Max I/O** - Systemy plików w trybie Max I/O mogą skalować się do wyższych poziomów łącznej przepustowości i operacji na sekundę z kompromisem w postaci nieco wyższych opóźnień dla operacji na metadanych plików;

- **Throughput mode**:

  - **Bursting** - domyślny - przepustowość skaluje się wraz ze wzrostem systemu plików;

  - **Provisioned** - można określić przepustowość systemu plików niezależnie od ilości przechowywanych danych.

---

# See also

- [[Elastic Block Store]]
- [[EC2 Instance Store]]
