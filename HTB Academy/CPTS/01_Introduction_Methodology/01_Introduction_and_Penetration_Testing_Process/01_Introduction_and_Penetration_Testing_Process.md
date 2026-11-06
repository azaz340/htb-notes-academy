
# Penetration Testing Process

## 1. Cel i ogólna zasada

**Penetration testing (pentest)** — kontrolowane testowanie zabezpieczeń w celu wykrycia i potwierdzenia podatności oraz określenia ich wpływu na organizację.

Proces nie jest sztywną checklistą. Etapy są **iteracyjne** — często wracamy do wcześniejszych faz, gdy pojawiają się nowe informacje.

**Główne etapy:**

1. `Pre-Engagement` — ustalenia i zgody

2. `Information Gathering` — zbieranie informacji

3. `Vulnerability Assessment` — analiza podatności

4. `Exploitation` — próba wykorzystania podatności

5. `Post-Exploitation` — działania po uzyskaniu dostępu

6. `Lateral Movement` — przemieszczanie się po sieci

7. `Proof-of-Concept (PoC)` — udokumentowanie i potwierdzenie wyników

8. `Post-Engagement` — raport, przekazanie wyników i sprzątanie


> **Pentest ≠ CTF.** W prawdziwym pentestcie liczą się dokładność, bezpieczeństwo systemów, dowody i jakość raportu — nie tylko jak najszybsze zdobycie flagi.

## 2. Pre-Engagement

Faza uzgodnień z klientem. Wszystkie istotne warunki muszą być jasno ustalone przed testami.

### Najważniejsze elementy

- `Goals` — cele testu.

- `Scope` — zakres: IP, domeny, URL-e, hosty, konta i inne zasoby objęte testem.

- `Rules of Engagement (RoE)` — zasady przeprowadzania testu.

- `Time Estimation` — termin, czas trwania i dozwolone okna testowe.

- `Methodology` — np. OSSTMM, PTES, OWASP.

- `Communication` — osoby kontaktowe i sposób zgłaszania incydentów.

- `Risk & Limitations` — ryzyka, ograniczenia i systemy krytyczne.

- `Information Handling` — sposób ochrony danych i dowodów.

- `Reporting` — forma raportu i odbiorcy.


### Ważne dokumenty

|Dokument|Cel|
|---|---|
|`NDA` — Non-Disclosure Agreement|Poufność informacji|
|`Scoping Questionnaire`|Zebranie wymagań klienta|
|`Scoping Document`|Podsumowanie zakresu|
|`SoW` — Scope of Work|Uzgodniony zakres prac|
|`RoE` — Rules of Engagement|Zasady, ograniczenia i przebieg testów|
|`Report`|Wyniki, dowody i zalecenia|

**Typy NDA:**

- `Unilateral` — poufność obowiązuje jedną stronę.

- `Bilateral` — obowiązuje obie strony.

- `Multilateral` — obejmuje więcej niż dwie strony.


### Typy pentestów według dostępnej wiedzy

|Typ|Informacje wejściowe|
|---|---|
|`Black Box`|Minimalne, np. adresy IP i domeny|
|`Grey Box`|Częściowe, np. URL-e, hosty i podsieci|
|`White Box`|Rozbudowane, np. konfiguracje, kod i dane dostępowe|
|`Red Teaming`|Symulacja działań przeciwnika; może obejmować social engineering i testy fizyczne|
|`Purple Teaming`|Współpraca z zespołem obrony|

Red Teaming i Purple Teaming mogą być łączone z różnymi modelami dostępu.

### Legal & Safety

- Uzyskaj **pisemną zgodę** osoby uprawnionej.

- Testuj wyłącznie zasoby objęte `scope`.

- Przestrzegaj ograniczeń i ustalonych terminów.

- Nie powoduj nieuzgodnionych zakłóceń ani utraty danych.

- Nie ujawniaj ani nie wykorzystuj danych w sposób nieobjęty zgodą.

- Ustal procedurę zgłaszania krytycznych podatności i przerwania testów.

- Testy obejmujące dostawców zewnętrznych wymagają odpowiednich zgód.


## 3. Information Gathering

**Cel:** poznać infrastrukturę, technologie, usługi i zależności, aby wskazać możliwe wektory ataku (`attack vectors`).

To etap, do którego wracamy wielokrotnie — również po uzyskaniu dostępu do hosta.

### Główne obszary

| Obszar                       | Co ustalamy                                                 |
| ---------------------------- | ----------------------------------------------------------- |
| `OSINT`                      | Publicznie dostępne informacje o organizacji i pracownikach |
| `Infrastructure Enumeration` | Domeny, DNS, IP, hosty, serwery i topologia                 |
| `Service Enumeration`        | Usługi, wersje i udostępniane informacje                    |
| `Host Enumeration`           | System operacyjny, porty, usługi i rola hosta               |

### OSINT — Open-Source Intelligence

Zbieranie informacji z publicznie dostępnych źródeł.

Potencjalne źródła informacji:

- publiczne repozytoria kodu, np. GitHub;

- dokumentacja i fragmenty kodu publikowane przez programistów;

- informacje o pracownikach, technologiach i strukturze organizacji;

- przypadkoo ujawnione hasła, hashe, klucze SSH i tokeny.


**Znaczenie:** dane ujawnione publicznie mogą prowadzić do uzyskania dostępu do systemów.

### Enumeration — najważniejsze rozróżnienie

- `Infrastructure Enumeration` — obraz całej infrastruktury.

- `Service Enumeration` — szczegóły konkretnych usług.

- `Host Enumeration` — szczegóły konkretnej maszyny.


W każdym przypadku ustalenia muszą być porównywane z zatwierdzonym `scope`.

## 4. Vulnerability Assessment

**Cel:** analizować zebrane informacje, identyfikować potencjalne podatności i ustalać, które wektory warto zweryfikować.

Obejmuje:

- automatyczne skanowanie znanych podatności;

- ręczną analizę konfiguracji i zachowania usług;

- badanie wersji oprogramowania;

- research znanych podatności i istniejących PoC;

- ocenę ryzyka oraz priorytetyzację.


### Vulnerability Research

Źródła wskazane w materiale:

- [NVD](https://nvd.nist.gov/)

- [CVE](https://www.cve.org/)

- [Exploit-DB](https://www.exploit-db.com/)

- [Vulners](https://vulners.com/)

- [Packet Storm](https://packetstormsecurity.com/)

- [CVE Details](https://www.cvedetails.com/)


**Uwaga:** wykryta wersja podatnego oprogramowania nie oznacza automatycznie, że podatność da się wykorzystać. Trzeba sprawdzić konfigurację, warunki podatności i możliwość jej potwierdzenia.

### Cztery typy analizy

|Typ|Pytanie|
|---|---|
|`Descriptive`|Co się wydarzyło / co obserwujemy?|
|`Diagnostic`|Dlaczego to się wydarzyło?|
|`Predictive`|Co może się wydarzyć?|
|`Prescriptive`|Jakie działanie należy podjąć?|

### Przykład myślenia analitycznego

Skan wykrywa otwarty port `2121/TCP`.

1. **Observation:** port TCP 2121 jest otwarty.
    
2. **Hypothesis:** może działać tam usługa podobna do FTP, ponieważ standardowy FTP korzysta z portu 21.
    
3. **Verification:** sprawdź usługę i odpowiedź serwera.
    
4. **Conclusion:** dopiero potwierdzone informacje stanowią podstawę dalszej analizy.
    

**Zasada:** oddzielaj to, co wiesz, od tego, co jedynie podejrzewasz.

### Priorytetyzacja ataków

Według materiału należy uwzględnić:

- `Probability of Success` — prawdopodobieństwo powodzenia;
    
- `Complexity` — trudność, czas i wysiłek;
    
- `Probability of Damage` — ryzyko uszkodzenia lub zakłócenia działania systemu.
    

Nie wybieraj automatycznie podatności o najwyższej teoretycznej wadze. Znaczenie mają również możliwość potwierdzenia, konfiguracja celu oraz ryzyko operacyjne.

Jeśli nie ma wystarczających informacji, wróć do `Information Gathering`.

## 5. Exploitation

**Cel:** zweryfikować podatność przez próbę jej wykorzystania i uzyskać uzgodniony poziom dostępu (`foothold`, uprawnienia itp.).

Podstawowy tok:

1. Wybierz wektor na podstawie wcześniejszej analizy.
    
2. Sprawdź warunki i dostępne `Proof of Concept`.
    
3. Przygotuj i dostosuj test do konkretnego środowiska.
    
4. Oceń możliwy wpływ na działanie celu.
    
5. Przeprowadź test wyłącznie w zatwierdzonym zakresie.
    
6. Zapisz działania i zbierz dowody.
    

### Przygotowanie exploitów

Jeżeli nie ma wiarygodnego PoC, materiał zaleca odtworzenie środowiska na lokalnej maszynie testowej (`VM`) z możliwie zgodnymi wersjami komponentów.

W razie wątpliwości dotyczących ryzyka należy skonsultować próbę z klientem lub osobą odpowiedzialną za test.

**Nie każda podatność musi być aktywnie wykorzystana.** Można opisać ją jako niepotwierdzoną w praktyce, jeżeli klient nie zgodził się na ryzykowny test.

## 6. Post-Exploitation

**Warunek:** mamy już dostęp do systemu.

**Cel:** poznać środowisko od wewnątrz, ocenić wpływ uzyskanego dostępu i — jeżeli mieści się to w zakresie testu — sprawdzić możliwość uzyskania wyższych uprawnień.

Główne obszary:

- `Information Gathering` — lokalne rozpoznanie.
    
- `Pillaging` — wyszukiwanie istotnych danych.
    
- `Vulnerability Assessment` — ponowna analiza z perspektywy lokalnej.
    
- `Privilege Escalation` — eskalacja uprawnień.
    
- `Persistence` — utrzymanie dostępu, jeśli jest dozwolone.
    
- `Data Exfiltration` — test możliwości wyprowadzenia danych, jeżeli został uzgodniony.
    
- `Evasive Testing` — ocena wykrywalności działań.
    

### Pillaging

Zbieranie informacji z już przejętego hosta.

Sprawdzane obszary obejmują:

- interfejsy sieciowe, routing, DNS, ARP i podsieci;
    
- usługi lokalne, udziały (`shares`) i połączenia z innymi hostami;
    
- skrypty, pliki konfiguracyjne, dokumenty i inne przechowywane dane;
    
- potencjalnie ujawnione poświadczenia (`credentials`) i klucze.
    

**Po co?** Aby pokazać realny wpływ podatności i ustalić, czy znalezione informacje umożliwiają dalszą eskalację uprawnień lub ruch w sieci.

### Privilege Escalation

Uzyskanie wyższych uprawnień niż aktualnie posiadane.

W materiale jako przykłady najwyższych uprawnień wymieniono:

- Linux: `root`;
    
- Windows: `SYSTEM`, `local administrator` lub uprawnienia administratora domeny — zależnie od celu.
    

Eskalacja może wynikać zarówno z lokalnej podatności, jak i z uzyskania poświadczeń użytkownika o wyższych uprawnieniach.

### Data Exfiltration

`Exfiltration` — przesłanie danych z systemu docelowego do środowiska testera.

- Przed transferem rzeczywistych danych wrażliwych należy uzyskać zgodę.
    
- Często można wykorzystać dane testowe (`synthetic / dummy data`), aby sprawdzić mechanizmy wykrywania.
    
- `DLP`, `EDR` i `Network Monitoring` mogą pomagać wykrywać lub blokować takie działania.
    
- W razie znalezienia danych wrażliwych należy postępować zgodnie z uzgodnioną procedurą i odpowiednio szybko poinformować klienta.
    

### Evasive Testing

Ocena tego, czy działania testera zostaną wykryte przez zabezpieczenia.

|Tryb|Sens|
|---|---|
|`Evasive`|Testowanie z naciskiem na ograniczenie wykrywalności|
|`Hybrid-Evasive`|Połączenie działań dyskretnych i jawnych, zgodnie z ustaleniami|
|`Non-Evasive`|Testowanie bez nacisku na unikanie wykrycia|

Wyniki pomagają ocenić monitoring, alertowanie oraz ewentualne `blind spots` w zabezpieczeniach.

## 7. Lateral Movement

**Cel:** przejść z hosta, do którego uzyskano dostęp, na kolejne systemy w sieci wewnętrznej.

Przykładowy łańcuch opisany w materiale:

`Initial Access → Enumeration → Credentials → Access to another host → Further assessment`

Nie zawsze trzeba najpierw uzyskać uprawnienia administratora na początkowym hoście — dalszy ruch może być możliwy przy obecnych uprawnieniach.

### Pivoting / Tunneling

Wykorzystanie przejętego hosta jako pośrednika do komunikacji z innymi segmentami sieci.

**Po co?** Aby dotrzeć do systemów, które nie są bezpośrednio osiągalne z maszyny testera.

### Przebieg lateral movement

1. `Pivoting` — uzyskanie drogi do innych segmentów sieci.
    
2. `Evasive Testing` — zgodnie z zakresem testu.
    
3. `Information Gathering` — rozpoznanie dostępnych hostów.
    
4. `Vulnerability Assessment` — analiza możliwych dróg dostępu.
    
5. `(Privilege) Exploitation` — weryfikacja wybranych wektorów.
    
6. `Post-Exploitation` — ponowne rozpoznanie kolejnego hosta.
    

Proces jest iteracyjny: na każdym kolejnym hoście można odkryć informacje umożliwiające dalszy ruch.

## 8. Proof of Concept (PoC)

**PoC** — dowód potwierdzający, że dana podatność istnieje i może prowadzić do określonego skutku.

PoC może mieć formę:

- dokumentacji kroków odtwarzających problem;
    
- zrzutów ekranu i innych dowodów;
    
- skryptu demonstrującego wykorzystanie podatności;
    
- opisu całego `attack chain`.
    

### Po co tworzy się PoC?

- Umożliwia administratorowi lub developerowi odtworzenie problemu.
    
- Pokazuje realny wpływ podatności.
    
- Ułatwia sprawdzenie, czy wdrożona poprawka działa.
    
- Pomaga ustalić kolejność napraw.
    

**Ważne:** naprawienie jednego sposobu wykorzystania podatności nie oznacza, że sam problem został usunięty.

Przykład z materiału: zmiana słabego hasła jednego administratora nie rozwiązuje problemu słabej `password policy`, jeśli inni użytkownicy nadal mogą ustawiać słabe hasła.

## 9. Post-Engagement

Końcowy etap współpracy z klientem.

Najważniejsze czynności:

- przygotowanie raportu technicznego i podsumowania dla kierownictwa;
    
- przedstawienie wyników (`report walkthrough`);
    
- przekazanie dowodów i zaleceń naprawczych;
    
- usunięcie pozostawionych artefaktów testowych zgodnie z ustaleniami;
    
- bezpieczne zarchiwizowanie danych zgodnie z umową;
    
- ewentualny `retest` — sprawdzenie, czy klient poprawnie usunął podatności.
    

## 10. Dokumentacja i praktyka

Dobre umiejętności techniczne nie wystarczą, jeśli nie potrafisz udowodnić wyników i jasno ich opisać.

### Technical documentation

Powinna umożliwiać technicznemu odbiorcy zrozumienie:

- co zostało znalezione;
    
- jak odtworzyć problem;
    
- jaki jest jego wpływ;
    
- jakie dowody potwierdzają ustalenia;
    
- jak można problem naprawić.
    

### Non-technical documentation

Powinna być zwięzła i zrozumiała dla osób bez głębokiej wiedzy technicznej. Skupia się na wpływie biznesowym, ryzyku i zalecanych działaniach.

### Proponowany cykl ćwiczeń HTB z materiału

1. Przerób moduły i wykonaj ćwiczenia.
    
2. Ponownie wykonaj moduł od początku, tworząc własne notatki.
    
3. Rozwiąż maszyny `Retired` i porównaj rozwiązanie z write-upem.
    
4. Sprawdź, jakich informacji zabrakło w notatkach.
    
5. Ćwicz na maszynach `Active` bez gotowych write-upów.
    
6. Przejdź do większych środowisk `Pro Labs / Endgames`, gdzie liczą się zależności między hostami i cały attack chain.
    
7. Dokumentuj zarówno techniczne ustalenia, jak i nietechniczne podsumowania.
    

---

## Szybka powtórka

|Pojęcie|Zapamiętaj|
|---|---|
|`Scope`|Co wolno testować|
|`Information Gathering`|Co wiemy o celu|
|`Vulnerability Assessment`|Co może być podatne i dlaczego|
|`Exploitation`|Czy podatność da się wykorzystać|
|`Post-Exploitation`|Co można osiągnąć po uzyskaniu dostępu|
|`Pillaging`|Jakie istotne informacje są dostępne na hoście|
|`Privilege Escalation`|Czy można uzyskać wyższe uprawnienia|
|`Lateral Movement`|Czy można przejść na kolejne hosty|
|`Pivoting`|Jak użyć hosta pośredniczącego do dotarcia do innych systemów|
|`PoC`|Jak udowodnić i odtworzyć problem|
|`Post-Engagement`|Jak przekazać wyniki i zakończyć test|

**Najważniejsza zasada:** pentest to nie tylko znalezienie exploita. To cały proces — od legalnego określenia zakresu, przez analizę i weryfikację, po udokumentowanie wpływu oraz przekazanie konkretnych zaleceń naprawczych.

## Źródło

[HTB Academy — Penetration Testing Process](https://academy.hackthebox.com/app/module/90/section/1559)