---
tags:
  - cybersecurity
  - incident-response
  - HTB
  - methodology
  - CDSA
  - SOC
source: HTB Academy – Incident Handling Process
---

# Incident Handling Process – Szybka Ściągawka

## 📌 TL;DR / Cheat Sheet
- **Definicja:** Ustrukturyzowany proces reagowania na naruszenia bezpieczeństwa w celu minimalizacji szkód i przywrócenia operacyjności.
- **Dwell Time:** Czas od momentu włamania do wykrycia przez SOC/IRT (kluczowy wskaźnik do skrócenia).
- **Złota zasada:** Proces jest iteracyjny – analiza dowodów może zmusić do powrotu do wcześniejszych faz.

---

## ⚡ Fazy Procesu (NIST SP 800-61 vs. SANS)

| Faza (NIST) | Faza (SANS) | Główny cel |
| :--- | :--- | :--- |
| **1. Preparation** | Preparation | Wdrożenie narzędzi (SIEM, EDR), procedur, logowania i szkoleń. |
| **2. Detection & Analysis** | Identification | Wykrycie anomalii, triaż (True/False Positive), ustalenie zakresu (Scope). |
| **3. Containment** | Containment | Powstrzymanie ataku (izolacja hosta, blokada sieciowa/IP). |
| **4. Eradication** | Eradication | Usunięcie malware, backdoorów i załatanie wektora początkowego. |
| **5. Recovery** | Recovery | Bezpieczny powrót systemów do produkcji + wzmożony monitoring. |
| **6. Post-Incident** | Lessons Learned | Spotkanie post-mortem, raport poincydentowy, aktualizacja reguł. |

---

## 🔍 Szczegółowy Przebieg Faz

### 1. Preparation (Przygotowanie)
- **Zasoby:** Zespół CSIRT, polityki, playbooki (np. Ransomware, Phishing).
- **Out-of-Band Communication:** Zabezpieczone kanały poza główną siecią korporacyjną na wypadek przejęcia komunikacji przez atakującego.

### 2. Detection & Analysis (Wykrywanie i Triaż)
- **Źródła:** Windows Event Logs (Sysmon), EDR, alerty IDS/Suricata, CTI (IoCs/TTPs).
- **4 Pytania Analityczne:**
  1. *Descriptive:* Co się stało?
  2. *Diagnostic:* Dlaczego się stało?
  3. *Predictive:* Co się stanie dalej?
  4. *Prescriptive:* Co musimy teraz zrobić?

### 3. Containment (Ograniczanie / Izolacja)
- **Short-Term:** Natychmiastowe odcięcie (izolacja EDR, wyłączenie portu switcha).
- **Long-Term:** Tymczasowe reguły zapór, blokady kont.
- *Ryzyko:* Nagłe odcięcie może uruchomić skrypty niszczące (Wiper/Ransomware), jeśli atakujący wykryje wykrycie.

### 4. Eradication (Usuwanie)
- **Akcje:** Usunięcie artefaktów, zamknięcie podatności, reset poświadczeń (Kerberos tickets, konta serwisowe).
- **Najlepsza praktyka:** *Re-building* (postawienie maszyny na nowo z czystego obrazu Gold Image) zamiast ręcznego czyszczenia.

### 5. Recovery (Przywracanie)
- **Kroki:** Odtwarzanie z czystych kopii zapasowych, hardening systemów, **Enhanced Monitoring** (okres wzmożonej czujności przez 14–30 dni).

### 6. Lessons Learned (Wnioski)
- **Raport:** Technical Walkthrough (dla techników) + Executive Summary (dla zarządu).
- **Spotkanie Post-Mortem:** Analiza co zadziałało, co zawiodło i jak dostosować reguły detekcji.

---
**Źródło:** *HTB Academy – Incident Handling Process*