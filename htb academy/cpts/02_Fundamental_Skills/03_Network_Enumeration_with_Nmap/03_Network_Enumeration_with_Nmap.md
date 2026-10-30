---

tags:

- cybersecurity
    
- pentesting
    
- enumeration
    
- nmap
    

---

# Enumeration & Nmap

## 1. Enumeration — najważniejsza faza

**Enumeration** = aktywne zbieranie szczegółowych informacji o usługach, systemach i dostępnych zasobach celu.

Cel nie polega wyłącznie na znalezieniu otwartego portu, ale na ustaleniu:

- jakie usługi działają,
    
- jakie mają wersje i konfiguracje,
    
- jakie informacje udostępniają,
    
- jakie mogą oferować attack vectors.
    

**Kluczowe zasady:**

- `Manual enumeration` jest równie ważne jak automatyczne skanery.
    
- Narzędzia nie zastępują rozumienia działania usług i protokołów.
    
- Błędna konfiguracja (`misconfiguration`) często ujawnia dodatkowe informacje.
    
- Brak odpowiedzi nie zawsze oznacza, że usługa jest niedostępna.
    

## 2. Nmap — Network Mapper

**Nmap** to narzędzie do network discovery i security auditing.

Pozwala na:

- wykrywanie aktywnych hostów (`Host Discovery`),
    
- skanowanie portów (`Port Scanning`),
    
- wykrywanie usług i wersji (`Service Detection`),
    
- rozpoznawanie systemu operacyjnego (`OS Detection`),
    
- interakcję z usługami przez skrypty (`NSE`).
    

Podstawowa składnia:

```
nmap <scan-type> <options> <target>
```

## 3. Host Discovery

Wykrywanie, które urządzenia są aktywne w sieci.

```
nmap -sn 192.168.1.0/24
```

|Opcja|Znaczenie|
|---|---|
|`-sn`|Host discovery bez port scanning|
|`-PE`|ICMP Echo Request (ping)|
|`-Pn`|Pomija host discovery — traktuje host jako aktywny|
|`-iL hosts.txt`|Wczytuje listę hostów z pliku|
|`--disable-arp-ping`|Wyłącza ARP ping|
|`--reason`|Pokazuje powód przypisania statusu|
|`--packet-trace`|Pokazuje wysyłane i odbierane pakiety|

**Uwaga:** host może nie odpowiadać na ICMP z powodu firewall rules, mimo że działa.

## 4. Port Scanning

### Najważniejsze techniki

|Opcja|Technika|Charakterystyka|
|---|---|---|
|`-sS`|TCP SYN Scan|Half-open scan; nie kończy 3-way handshake|
|`-sT`|TCP Connect Scan|Pełny 3-way handshake|
|`-sU`|UDP Scan|Wolniejszy, często brak odpowiedzi|
|`-sA`|TCP ACK Scan|Pomaga określić filtrowanie przez firewall|

### TCP SYN Scan (`-sS`)

Wysyła pakiet z flagą `SYN`, ale nie kończy połączenia.

- `SYN-ACK` → port `open`
    
- `RST` → port `closed`
    
- brak odpowiedzi → zwykle `filtered`
    

### TCP Connect Scan (`-sT`)

Wykonuje pełny TCP three-way handshake:

1. `SYN`
    
2. `SYN-ACK`
    
3. `ACK`
    

Jest dokładny, ale łatwiejszy do wykrycia, ponieważ ustanawia pełne połączenie.

### UDP Scan (`-sU`)

UDP nie wykorzystuje three-way handshake, dlatego skanowanie jest wolniejsze.

- odpowiedź UDP → `open`
    
- ICMP Port Unreachable → `closed`
    
- brak odpowiedzi → `open|filtered`
    

## 5. Port States

Nmap rozpoznaje sześć stanów portów:

|State|Znaczenie|
|---|---|
|`open`|Usługa przyjmuje połączenia|
|`closed`|Port jest osiągalny, ale nie działa na nim usługa|
|`filtered`|Firewall lub filtr uniemożliwia określenie stanu|
|`unfiltered`|Port osiągalny, ale nie wiadomo, czy jest open czy closed|
|`open|filtered`|Nie można rozróżnić open od filtered|
|`closed|filtered`|Nie można rozróżnić closed od filtered|

## 6. Wybór portów

|Opcja|Znaczenie|
|---|---|
|`-p 22,80,443`|Konkretne porty|
|`-p 1-1000`|Zakres portów|
|`-p-`|Wszystkie porty TCP (1–65535)|
|`--top-ports 10`|10 najpopularniejszych portów|
|`-F`|Szybki scan — top 100 portów|

Domyślnie Nmap skanuje top 1000 TCP ports.

## 7. Service Enumeration

Po znalezieniu otwartych portów warto sprawdzić, jakie usługi i wersje na nich działają.

```
nmap -sV <target>
```

`-sV` = Service Version Detection.

Przykładowe informacje:

- `22/tcp` → OpenSSH,
    
- `80/tcp` → Apache HTTP Server,
    
- `445/tcp` → Samba.
    

### Banner Grabbing

**Banner** to informacje wysyłane przez usługę po nawiązaniu połączenia, np. nazwa serwera, wersja lub system operacyjny.

```
nc -nv <target> 25
```

Przydatne narzędzia:

- `nc` / `ncat` — ręczne połączenie z usługą,
    
- `tcpdump` — przechwytywanie i analiza ruchu.
    

Nie wszystkie informacje muszą być widoczne w automatycznym wyniku Nmap.

## 8. Nmap Scripting Engine (NSE)

NSE umożliwia wykonywanie skryptów napisanych w Lua, które komunikują się z usługami i zbierają dodatkowe informacje.

### Przydatne kategorie

|Category|Zastosowanie|
|---|---|
|`auth`|Informacje o uwierzytelnianiu|
|`brute`|Próby logowania metodą brute-force|
|`default`|Domyślne skrypty|
|`discovery`|Dodatkowe informacje o usługach|
|`exploit`|Próby wykorzystania znanych podatności|
|`safe`|Skrypty uznawane za bezpieczne|
|`vuln`|Wykrywanie znanych podatności|
|`version`|Rozszerzenie wykrywania wersji|
|`intrusive`|Skrypty mogące negatywnie wpłynąć na system|

### Uruchamianie skryptów

```
nmap -sC <target>
```

Domyślne skrypty (`default`).

```
nmap --script vuln <target>
```

Skrypty z kategorii `vuln`.

```
nmap --script banner,smtp-commands -p 25 <target>
```

Wybrane skrypty.

### Aggressive Scan

```
nmap -A <target>
```

Łączy:

- `-sV` — service detection,
    
- `-O` — OS detection,
    
- `--traceroute` — traceroute,
    
- `-sC` — default NSE scripts.
    

## 9. Saving Results

Wyniki skanowania warto zapisywać, aby móc je później porównywać i analizować.

|Opcja|Format|
|---|---|
|`-oN file`|Normal output|
|`-oG file`|Grepable output|
|`-oX file`|XML output|
|`-oA file`|Wszystkie powyższe formaty|

```
nmap -p- -oA scan_results <target>
```

XML można przekształcić w czytelny raport HTML:

```
xsltproc scan_results.xml -o report.html
```

## 10. Performance — szybkość skanowania

Szybszy scan nie zawsze oznacza lepszy scan. Zbyt agresywne ustawienia mogą powodować pominięcie hostów lub portów.

|Opcja|Znaczenie|
|---|---|
|`-T0`|Paranoid — bardzo wolny|
|`-T1`|Sneaky|
|`-T2`|Polite|
|`-T3`|Normal — domyślny|
|`-T4`|Aggressive|
|`-T5`|Insane — bardzo agresywny|
|`--max-retries 3`|Maksymalna liczba ponowień|
|`--min-rate 300`|Minimalna liczba pakietów na sekundę|
|`--initial-rtt-timeout 50ms`|Początkowy timeout|
|`--max-rtt-timeout 100ms`|Maksymalny timeout|

**RTT (Round-Trip Time)** = czas potrzebny na otrzymanie odpowiedzi na wysłany pakiet.

Pamiętaj: skrócenie timeoutów i ograniczenie retries może sprawić, że Nmap nie wykryje wszystkich aktywnych portów.

## 11. Firewall, IDS i IPS

### Firewall

Kontroluje ruch sieciowy na podstawie reguł. Może:

- `drop` — ignorować pakiety bez odpowiedzi,
    
- `reject` — odrzucać pakiety i zwracać odpowiedź.
    

### IDS vs IPS

|Mechanizm|Funkcja|
|---|---|
|`IDS`|Wykrywa podejrzany ruch i generuje alerty|
|`IPS`|Wykrywa i automatycznie blokuje podejrzany ruch|

### ACK Scan (`-sA`)

```
nmap -sA <target>
```

Wysyła pakiety z flagą `ACK`. Pomaga sprawdzić, czy port jest filtrowany przez firewall.

Wyniki:

- `unfiltered` — pakiet dotarł do hosta i otrzymano RST,
    
- `filtered` — firewall blokuje lub filtruje ruch.
    

ACK scan nie określa, czy port jest open czy closed.

### Wybrane techniki z materiału

|Opcja|Zastosowanie|
|---|---|
|`-D RND:5`|Dodaje losowe adresy decoy|
|`-S <IP>`|Ustawia source IP|
|`-e tun0`|Wskazuje interfejs sieciowy|
|`--source-port 53`|Ustawia port źródłowy|
|`--dns-server <IP>`|Wskazuje serwer DNS|

Techniki z tej sekcji służą do testowania zachowania firewall/IDS/IPS w ramach autoryzowanego pentestu. Nie należy zakładać, że decoys lub zmiana source port automatycznie zapewnią niewykrywalność.

---

## 12. Najważniejsze komendy — Cheat Sheet

```
# Host discovery
nmap -sn 192.168.1.0/24

# Szybki scan
nmap -F <target>

# Top 1000 TCP ports (domyślnie)
nmap <target>

# Wszystkie TCP ports
nmap -p- <target>

# Konkretne porty
nmap -p 22,80,443 <target>

# SYN scan
sudo nmap -sS <target>

# Connect scan
nmap -sT <target>

# UDP scan
sudo nmap -sU <target>

# Service version detection
nmap -sV <target>

# Default NSE scripts
nmap -sC <target>

# Vulnerability scripts
nmap --script vuln <target>

# OS detection
sudo nmap -O <target>

# Aggressive scan
sudo nmap -A <target>

# Zapis wyników we wszystkich formatach
nmap -oA results <target>
```

## 13. Workflow — kolejność działania

1. **Host Discovery** — ustal, które hosty są aktywne.
    
2. **Quick Port Scan** — znajdź najważniejsze otwarte porty.
    
3. **Full Port Scan** — sprawdź wszystkie porty TCP (`-p-`).
    
4. **Service Detection** — ustal usługi i ich wersje (`-sV`).
    
5. **Manual Enumeration** — ręcznie sprawdź działanie usług i bannery.
    
6. **NSE** — wykorzystaj odpowiednie skrypty do zebrania dodatkowych informacji.
    
7. **Vulnerability Assessment** — sprawdź znane podatności i możliwe attack vectors.
    
8. **Save Results** — zapisz wyniki do późniejszej analizy.
    

---

## Do zapamiętania

- Enumeration to przede wszystkim rozumienie usług, nie samo uruchamianie narzędzi.
    
- `-sS` = SYN scan, `-sT` = pełne połączenie TCP, `-sU` = UDP.
    
- `filtered` nie oznacza automatycznie, że port jest zamknięty.
    
- `-sV` pozwala rozpoznać wersje usług.
    
- `-sC` uruchamia domyślne NSE scripts.
    
- `-A` łączy kilka technik wykrywania.
    
- `-p-` skanuje wszystkie porty TCP.
    
- Szybszy scan może oznaczać mniej dokładne wyniki.
    
- IDS wykrywa, IPS może automatycznie blokować.
    
- Manual enumeration pozwala znaleźć informacje pominięte przez automatyczne narzędzia.
    

