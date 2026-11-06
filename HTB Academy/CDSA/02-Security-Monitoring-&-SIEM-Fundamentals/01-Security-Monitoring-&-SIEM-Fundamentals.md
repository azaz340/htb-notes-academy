# 🛡️ SIEM / SOC / ELK – Cheat Sheet (Obsidian Edition)

> [!IMPORTANT]  
> SIEM ≠ „magiczne AI”
> 
> To:
> 
> - zbieranie logów
>     
> - korelacja zdarzeń
>     
> - wykrywanie ataków
>     
> - reakcja na incydenty
>     

---

# 🧠 1. Czym jest SIEM?

## 📖 Definicja

**SIEM (Security Information and Event Management)**  
= centrum dowodzenia bezpieczeństwem

Łączy:

|Typ|Rola|
|---|---|
|**SIM**|przechowywanie + raportowanie logów|
|**SEM**|analiza zdarzeń w czasie rzeczywistym|

---

## 🎯 Główne zadania SIEM

### 📥 Agregacja

Zbieranie logów z:

- firewalli
    
- Windowsów
    
- Linuxów
    
- baz danych
    
- aplikacji
    

---

### 🔄 Normalizacja

Zamiana różnych logów na wspólny format

➡️ np. JSON + wspólny timestamp

---

### 🧠 Korelacja

Łączenie zdarzeń w całość

## 💣 Przykład

```id="9b2f7x"
4625 (failed login)
+
password reset
+
duży outbound traffic
=
ALERT
```

---

# 🥞 2. Elastic Stack (ELK)

> [!TIP]  
> Najpopularniejszy stack SIEM/SOC

## 🔁 Flow danych

```id="n15m1v"
Beats → Logstash → Elasticsearch → Kibana
```

---

## 🧩 Komponenty

|Komponent|Rola|Opis|
|---|---|---|
|Beats|Shipper|wysyła logi|
|Logstash|Processor|transformuje dane|
|Elasticsearch|Storage/Search|indeksowanie + wyszukiwanie|
|Kibana|Dashboard|GUI + analityka|

---

# 📡 3. Data Flow (jak lecą logi)

## 🔹 1. Ingestion

Pobranie logów przez:

- Filebeat
    
- Winlogbeat
    
- Auditbeat
    

---

## 🔹 2. Normalization

Logstash:

- parsuje log
    
- robi JSON
    
- dodaje metadata
    

---

## 🔹 3. Indexing

Elasticsearch:

- indeksuje dane
    
- pozwala przeszukiwać miliony logów w ms
    

---

## 🔹 4. Analysis

SOC Analyst:

- szuka IOC
    
- filtruje alerty
    
- robi incident response
    

---

# 🔍 4. Kluczowe pojęcia

## ❌ False Positive (FP)

Legalne działanie oznaczone jako atak

---

## 🧠 Contextualization

Dodawanie kontekstu:

- kto
    
- gdzie
    
- kiedy
    
- z jakiego hosta
    

➡️ pomaga odróżnić FP od realnego ataku

---

## 😵 Alert Fatigue

Za dużo alertów = analityk ignoruje alarmy

> klasyczny problem SOC

---

## ⏱️ MTTD / MTTR

|Skrót|Znaczenie|
|---|---|
|MTTD|czas wykrycia ataku|
|MTTR|czas reakcji|

---

# 🛠️ 5. Sysmon Event ID 3

## 📡 Połączenie sieciowe

```json
{
  "event_id": 3,
  "image": "C:\\Windows\\System32\\cmd.exe",
  "destination_ip": "103.112.60.117",
  "port": 443,
  "user": "NT AUTHORITY\\SYSTEM"
}
```

---

## 🚨 Dlaczego podejrzane?

### ⚠️ `cmd.exe`

robi połączenie outbound

### ⚠️ port 443

udaje normalny HTTPS

### ⚠️ SYSTEM

wysokie uprawnienia

➡️ możliwy:

- malware
    
- beacon
    
- C2
    

---

# ⚖️ 6. Compliance

## 📜 Najważniejsze standardy

|Standard|Cel|
|---|---|
|GDPR / RODO|dane osobowe|
|PCI DSS|karty płatnicze|
|HIPAA|dane medyczne|

---

## 🎯 Dlaczego SIEM ważny?

Bo:

- loguje działania
    
- robi audyty
    
- daje raporty dla compliance
    

---

# 🧠 7. Triki SOC Analysty

## 🔥 Correlation Rules

Alert dopiero gdy:

- 10 failed loginów
    
- w 1 minutę
    
- z jednego IP
    

➡️ mniej FP

---

## 🌍 Threat Intel

Podpinanie:

- IOC feedów
    
- złośliwych IP
    
- hashy malware
    

---

## 🤖 AI / ML

Nowoczesny SIEM:

- uczy się zachowań
    
- wykrywa anomalie
    

## 💣 Przykład

Księgowa odpala PowerShell o 3 rano.

➡️ dziwne  
➡️ alert

---

# 🏛️ 8. Architektura i Skalowanie

## 📦 Buffering

Chroni przed utratą danych

### Najczęściej:

- Kafka
    
- Redis
    
- RabbitMQ
    

---

## 🔐 Reverse Proxy

### Nginx

- auth
    
- TLS
    
- dodatkowa warstwa ochrony
    

---

# 🛠️ 9. KQL (Kibana Query Language)

> [!IMPORTANT]  
> KQL = podstawowe narzędzie SOC Analysty

---

# 🔎 Podstawowe query

## 🔹 Event ID

```id="f8uqry"
event.code:4625
```

➡️ failed login Windows

---

## 🔹 Free text

```id="jqv5bf"
"svc-sql1"
```

➡️ szuka wszędzie

---

## 🔹 Logika

```id="l8yjlwm"
event.code:4625 AND user.name:administrator
```

---

## 🔹 Zakres czasu

```id="d6hy7m"
@timestamp >= "2026-05-01"
```

---

## 🔹 Wildcard

```id="pbbqq9"
user.name:admin*
```

---

# 🪟 10. Windows Event IDs (must know)

|Event ID|Znaczenie|
|---|---|
|4624|udane logowanie|
|4625|failed login|
|4688|process creation|
|4720|utworzenie usera|
|7045|nowa usługa|

---

## 🚨 Podejrzane statusy

```id="a7oqz9"
winlog.event_data.SubStatus:0xC0000072
```

➡️ próba logowania na disabled account

---

# 🌍 11. ECS (Elastic Common Schema)

## 🧠 Po co ECS?

Bo każdy vendor ma inne nazwy pól.

ECS robi wspólny język.

---

## 🔥 Zamiast:

```id="gncmx7"
src_ip
sourceip
client_ip
```

masz:

```id="qjlwmz"
source.ip
```

---

## 🎯 Zalety

- łatwiejsze query
    
- lepsza korelacja
    
- uniwersalne dashboardy
    
- mniej bólu
    

---

# 🛡️ 12. SOC (Security Operations Center)

## 🎯 Czym jest?

Zespół monitorujący bezpieczeństwo 24/7

---

## 🧰 Główne narzędzia

- SIEM
    
- IDS / IPS
    
- EDR
    
- SOAR
    

---

# 👥 13. Role w SOC

|Rola|Zadanie|
|---|---|
|Tier 1|triage alertów|
|Tier 2|analiza incydentów|
|Tier 3|threat hunting|
|Detection Engineer|reguły detekcji|
|SOC Manager|zarządzanie|

---

# 🔄 14. Incident Response Flow

## 🔹 1. Triage

Czy alert jest prawdziwy?

---

## 🔹 2. Containment

Odcięcie hosta od sieci

---

## 🔹 3. Elimination

Usunięcie malware

---

## 🔹 4. Recovery

Przywrócenie systemów

---

# 📈 15. Ewolucja SOC

## 🏚️ SOC 1.0

- perimeter focus
    
- dużo ręcznej pracy
    
- brak korelacji
    

---

## 🧠 SOC 2.0

- threat intel
    
- analiza anomalii
    
- behavioral analytics
    

---

## 🤖 Cognitive SOC

AI/ML:

- automatyzacja
    
- scoring alertów
    
- wspomaganie decyzji
    

---

# 💣 Esencja (to zapamiętaj)

- SIEM = logi + korelacja
    
- SOC = ludzie + procesy
    
- ELK = najpopularniejszy stack
    
- KQL = broń analityka
    
- FP = największy wróg SOC
    
- dobre reguły > dużo reguł
    
- kontekst = wszystko
    

---

# 🚀 Quick Commands / Queries

## 🔍 Failed logins

```id="g4xjlwm"
event.code:4625
```

---

## 🔍 Powershell execution

```id="sls0sn"
process.name:powershell.exe
```

---

## 🔍 RDP logins

```id="6k8p98"
event.code:4624 AND winlog.logon.type:10
```

---

## 🔍 Suspicious outbound traffic

```id="7x4t2q"
destination.port:443 AND process.name:cmd.exe
```

---

# 🧠 Mental Model

```id="lbvv6r"
Endpoint → Logs → SIEM → Correlation → Alert → Analyst → Response
```

---

To już wygląda jak notatka człowieka z SOC, a nie losowy dump z kursu 😎