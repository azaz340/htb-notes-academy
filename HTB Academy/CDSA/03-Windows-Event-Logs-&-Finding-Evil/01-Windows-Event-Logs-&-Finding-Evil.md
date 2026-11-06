## 1. System Logs

Logi dotyczące działania systemu Windows, uruchamiania, zamykania i usług.

|Event ID|Znaczenie|Znaczenie dla SOC|
|---|---|---|
|**1074**|System shutdown/restart|Kto i dlaczego wyłączył lub zrestartował system|
|**6005**|Event Log Service started|Uruchomienie usługi logowania, często przy starcie systemu|
|**6006**|Event Log Service stopped|Zatrzymanie usługi logowania, zwykle przy zamykaniu systemu|
|**6013**|System uptime|Czas działania systemu; może pomóc wykryć nieoczekiwany restart|
|**7040**|Service startup type changed|Zmiana trybu uruchamiania usługi (np. Manual → Automatic)|

## 2. Security Logs

Najważniejsze logi z perspektywy **authentication, privilege escalation, persistence i defense evasion**.

### Authentication & Logon

|Event ID|Znaczenie|Co może wykrywać|
|---|---|---|
|**4624**|Successful Logon|Nietypowe logowania, godziny lub lokalizacje|
|**4625**|Failed Logon|Brute-force, password spraying|
|**4648**|Explicit Credentials Used|Użycie jawnie podanych poświadczeń; możliwy lateral movement|
|**4672**|Special Privileges Assigned|Logowanie z wysokimi uprawnieniami|
|**4771**|Kerberos Pre-authentication Failed|Nieudane uwierzytelnianie Kerberos|
|**4776**|Credential Validation|Walidacja poświadczeń przez domain controller|

### Account & Privilege Changes

|Event ID|Znaczenie|Co może wykrywać|
|---|---|---|
|**4738**|User Account Changed|Zmiany konta, uprawnień lub członkostwa w grupach|
|**4719**|Audit Policy Changed|Zmiana polityki audytu, możliwe ograniczanie logowania zdarzeń|
|**4656**|Object Handle Requested|Próba uzyskania dostępu do pliku, rejestru lub innego obiektu|

### Persistence – Scheduled Tasks & Services

|Event ID|Znaczenie|Co może wykrywać|
|---|---|---|
|**4698**|Scheduled Task Created|Utworzenie zadania — możliwy persistence|
|**4700**|Scheduled Task Enabled|Włączenie zadania|
|**4701**|Scheduled Task Disabled|Wyłączenie zadania|
|**4702**|Scheduled Task Updated|Modyfikacja zadania|
|**7045**|Service Installed|Instalacja nowej usługi — możliwy persistence lub malware|

### Defense Evasion & Antivirus

|Event ID|Znaczenie|Co może wykrywać|
|---|---|---|
|**1102**|Audit Log Cleared|Czyszczenie Security Log — możliwa próba zatarcia śladów|
|**1116**|Malware Detected|Wykrycie malware przez Microsoft Defender|
|**1118**|Remediation Started|Rozpoczęcie usuwania lub kwarantanny malware|
|**1119**|Remediation Succeeded|Udana remediacja|
|**1120**|Remediation Failed|Nieudana remediacja|
|**5001**|Defender Real-time Protection Changed|Zmiana konfiguracji ochrony w czasie rzeczywistym|

### Network & File Shares

|Event ID|Znaczenie|Co może wykrywać|
|---|---|---|
|**5140**|Network Share Accessed|Dostęp do udziału sieciowego|
|**5142**|Network Share Added|Utworzenie nowego udziału sieciowego|
|**5145**|Network Share Access Check|Sprawdzenie, czy klient może uzyskać określony dostęp|
|**5157**|Connection Blocked|Zablokowanie połączenia przez Windows Filtering Platform|

---

## 3. Najważniejsze powiązania w analizie SOC

|Obszar|Event IDs|
|---|---|
|Logon monitoring|`4624`, `4625`, `4648`|
|Privileged accounts|`4672`|
|Kerberos attacks|`4771`, `4776`|
|Account changes|`4738`|
|Persistence|`4698`, `4700`, `4701`, `4702`, `7045`|
|Defense evasion|`1102`, `4719`, `5001`|
|Malware detection|`1116`, `1118`, `1119`, `1120`|
|Network shares|`5140`, `5142`, `5145`|
|Blocked connections|`5157`|

## 4. SOC Investigation – na co zwracać uwagę?

- **4625:** wiele nieudanych logowań w krótkim czasie może wskazywać na brute-force.
    
- **4624:** sprawdzaj konto, czas, źródło logowania i typ logowania.
    
- **4672:** zweryfikuj, czy wysokie uprawnienia są uzasadnione.
    
- **4698 / 4702:** sprawdź nazwę zadania, jego akcję i konto wykonujące operację.
    
- **7045:** zweryfikuj nazwę usługi, ścieżkę pliku i konto uruchamiające usługę.
    
- **1102:** ustal, kto wyczyścił logi i co działo się bezpośrednio wcześniej.
    
- **5001:** sprawdź, czy zmiana ochrony Defendera była autoryzowana.
    
- **5140 / 5145:** analizuj dostęp do udziałów, szczególnie nietypowy dostęp do zasobów.
    

> **Ważne:** pojedynczy Event ID nie potwierdza ataku. To sygnał do korelacji z innymi zdarzeniami, użytkownikiem, hostem, timestampem i kontekstem.