# 🏛️ Infosec & CIA Triad

**Infosec ≠ hacking**  
To przede wszystkim **zarządzanie ryzykiem danych**

## 🔺 CIA Triad

- **Confidentiality** → kto _może_ zobaczyć dane
- **Integrity** → czy dane _nie zostały zmienione_
- **Availability** → czy system _działa i jest dostępny_

> [!IMPORTANT]  
> Każda podatność narusza **minimum jeden filar (C/I/A)**

---

# 📉 Risk Management (praktyka)

**Bezpieczeństwo = kompromis biznes vs security**

## 🔁 Proces (5 kroków)

1. **Identify** → co może pójść źle
2. **Analyze** → jak duży impact
3. **Evaluate** → priorytet
    - Accept / Avoid / Control / Transfer
4. **Treat** → naprawa / mitigacja
5. **Monitor** → ciągła obserwacja

---

# 🥊 Red vs Blue

- 🔴 **Red Team** → atak (pentest, exploity)
- 🔵 **Blue Team** → obrona (SIEM, monitoring, IR)

> [!TIP]  
> Myśl jak **Red**, raportuj jak **Blue**

---

# 🐧 Lab Setup (VM)

> [!WARNING]  
> **Nigdy nie pracuj na host OS**

## 🧰 Hypervisory

- VirtualBox
- VMware
- Proxmox

## 📦 ISO vs OVA

- **ISO** → instalacja od zera
- **OVA** → gotowy system (HTB, Kali)

## ⚠️ Zasada

> 1 projekt = 1 VM  
> Mieszanie środowisk = proszenie się o syf

---

# 🔐 VPN (HTB + real world)

## 🧠 Jak działa

- ruch → przez serwer VPN
- dostajesz inne IP
- jesteś „w środku sieci”

## 🔌 Typy

- **Client VPN** → pełny dostęp
- **SSL VPN** → przez przeglądarkę

> [!WARNING]  
> VPN ≠ anonimowość  
> VPN = zmiana tego, **kto widzi Twój ruch**

---

## 🧪 HTB – szybkie komendy

```
sudo openvpn user.ovpn
```

✔ `Initialization Sequence Completed` → OK

```
ifconfig
```

➡️ szukaj `tun0`

```
netstat -rn
```

➡️ routing do labu

---

## ⚠️ Zasady

- używaj VM
- traktuj sieć jako **hostile**
- brak danych prywatnych
- nie używaj jednej VM do wszystkiego

---

# 📂 Organizacja pracy

> [!IMPORTANT]  
> Hackowanie = 30%  
> Dokumentacja = 70%

## 📁 Struktura

```
Projects/└── Client/    ├── scans/    ├── evidence/    ├── scope/    └── logs/
```

---

# 📝 Notatki

## 🧰 Narzędzia

- Obsidian ⭐
- Notion (uwaga na cloud)
- CherryTree

## 📌 Co zapisywać

- checklisty
- komendy
- payloady
- podatności

---

# 🛠️ Rola pentestera

Nie jesteś „hakerem z filmu”.

## 🎯 Twoja robota

1. znaleźć problem
2. pokazać exploit
3. zaproponować fix

---

# 💻 Podstawowe pojęcia

## 🐚 Shell

Dostęp do systemu

- Reverse → target łączy się do Ciebie
- Bind → Ty łączysz się do targeta
- Web → przez HTTP

---

## 🚪 Porty

Port = „drzwi” do usługi

### 🔑 Najważniejsze

|Port|Usługa|
|---|---|
|21|FTP|
|22|SSH|
|23|Telnet|
|25|SMTP|
|80|HTTP|
|443|HTTPS|
|445|SMB|
|3389|RDP|

---

## 🔄 TCP vs UDP

- **TCP** → stabilny, połączeniowy
- **UDP** → szybki, bez gwarancji

---

## 🌐 Web Server

- przyjmuje request
- zwraca response

---

## 🔥 OWASP Top 10

- Broken Access Control
- Injection
- Auth Failures
- Misconfiguration
- SSRF

> [!TIP]  
> ~80% CTF / HTB = web + OWASP

---

# 🧰 Narzędzia

## 🔐 SSH

```
ssh user@ip
```

## 📡 Netcat

```
nc IP PORT
```

## 🧪 Socat

- stabilniejsze shelle niż nc

## 🧱 Tmux

- `CTRL + B` → prefix
- `c` → nowe okno
- `%` → split pion
- `"` → split poziom

## ✍️ Vim

- `i` → insert
- `esc` → normal
- `:wq` → save + exit

---

# 💣 Esencja

- VPN = dostęp
- Port = wejście
- Usługa = powierzchnia ataku
- Shell = kontrola

---

# 🔎 Service Scanning (ENUMERATION)

> [!IMPORTANT]  
> **Najpierw scan, potem exploit**

---

## 🧠 O co chodzi

- host → IP
- usługi → porty
- cel → znaleźć **co działa i gdzie**

---

## 🚪 Zakres portów

- `1–65535`
- `1–1023` → well-known
- `0` → wildcard

---

## 🧰 Nmap

### 🔹 Quick scan

```
nmap IP
```

### 🔥 Full scan (must-have)

```
nmap -sC -sV -p- IP
```

- `-sC` → default scripts
- `-sV` → wersje
- `-p-` → wszystkie porty

> [!WARNING]  
> Wolne, ale daje pełny obraz

---

## 📊 Interpretacja

```
PORT   STATE SERVICE VERSION22/tcp open  ssh     OpenSSH 8.280/tcp open  http    Apache 2.4.41
```

- PORT → gdzie
- STATE → czy open
- SERVICE → co
- VERSION → czy podatne

---

## 🧠 Fingerprinting

- 22 → Linux vibe
- 3389 → Windows
- 445 → SMB = Windows

> tylko wskazówki — nie prawda objawiona

---

## 🛠️ NSE

```
nmap --script=banner -p21 IP
```

---

## 🎯 Banner Grabbing

```
nc -nv IP PORT
```

---

# 📡 Typowe usługi

## 📁 FTP (21)

```
ftp IP
```

login:

```
anonymous
```

🔍 szukaj:

- pliki
- backupy
- hasła

---

## 🪟 SMB (445)

```
smbclient -N -L \\IP
```

```
smbclient -U user \\IP\share
```

🔍 szukaj:

- passwords.txt
- configi
- backupy

---

## 📡 SNMP (161)

```
snmpwalk -v 2c -c public IP
```

⚠️ klasyk:

- `public` / `private`

---

### 💣 Bruteforce

```
onesixtyone -c dict.txt IP
```

---

# ⚔️ Mindset

> [!IMPORTANT]  
> Nie szukasz exploita. Szukasz **wejścia**.

## 🔁 Flow

1. scan
2. identyfikacja usługi
3. sprawdzenie:
    - anon access
    - weak creds
    - misconfig
4. exploit

---

## ❌ Błędy początkujących

- od razu exploit
- ignorowanie SMB/FTP
- brak `-p-`
- brak notatek

---

# 🚀 TL;DR (wersja brutalna)

- Nmap = oczy
- Usługi = drzwi
- Dane = klucze
- Shell = koniec gry