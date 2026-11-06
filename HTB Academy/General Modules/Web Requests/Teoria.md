## 📑 1. Struktura URL

Zasoby HTTP są dostępne przez **URL** (Uniform Resource Locator).

| Komponent     | Przykład            | Opis                                            |
| ------------- | ------------------- | ----------------------------------------------- |
| **Scheme**    | `https://`          | Protokół (zakończony `://`)                     |
| **User Info** | `admin:pass@`       | Opcjonalne poświadczenia (oddzielone `:` i `@`) |
| **Host**      | `inlanefreight.com` | Lokalizacja zasobu (Hostname lub IP)            |
| **Port**      | `:443`              | Domyślnie: **80** (HTTP), **443** (HTTPS)       |
| **Path**      | `/index.php`        | Ścieżka do konkretnego pliku/katalogu           |
| **Query**     | `?id=1&s=test`      | Parametry po `?`, rozdzielone `&`               |
| **Fragment**  | `#sekcja1`          | Lokalizacja wewnątrz strony (klient-side)       |

---

## 🛡️ 2. HTTPS (HTTP Secure)

Główna różnica: **Szyfrowanie**. HTTP przesyła dane otwartym tekstem (_clear-text_), co naraża użytkownika na ataki **MiTM** (Man-in-the-Middle).

> [!IMPORTANT] Dlaczego HTTPS?
> 
> - **Szyfrowanie:** Dane są nieczytelne dla osób trzecich.
> - **Integralność:** Chroni przed modyfikacją danych w locie.
> - **Autentykacja:** Certyfikaty SSL/TLS potwierdzają tożsamość serwera.

Handshake HTTPS (Flow):

1. **Redirect:** Klient wchodzi na port 80 -> Serwer wysyła `301 Moved Permanently` na port 443.
2. **Client Hello:** Klient wysyła obsługiwane algorytmy szyfrowania.
3. **Server Hello:** Serwer wysyła certyfikat i klucz publiczny.
4. **Key Exchange:** Weryfikacja certyfikatu i ustalenie kluczy sesyjnych.
5. **Encrypted Communication:** Rozpoczęcie bezpiecznej transmisji.

---

## 🛠️ 3. Praca z cURL

Narzędzie `curl` automatycznie obsługuje HTTPS, ale wymaga flag w specyficznych sytuacjach:

- **Pobranie strony:** `curl https://example.com`
- **Ignorowanie błędnych certyfikatów:**
    
    bash
    
    ```
    curl -k https://target-site.com
    ```
    
    Używaj kodu z rozwagą.
    
    _(Użyteczne przy testowaniu lokalnych aplikacji z certyfikatami self-signed)._

---

## ⚠️ 4. Uwagi Dotyczące Bezpieczeństwa

> [!WARNING] Atak Downgrade  
> Atakujący może próbować wymusić przejście z HTTPS na HTTP (np. przez proxy MITM). Nowoczesne przeglądarki chronią przed tym mechanizmem **HSTS**.

> [!TIP] Prywatność DNS  
> Samo HTTPS szyfruje treść, ale zapytania DNS mogą być widoczne. Rozwiązania:
> 
> - **DoH / DoT** (DNS over HTTPS/TLS)
> - **VPN**
> - Serwery: `1.1.1.1` (Cloudflare) lub `8.8.8.8` (Google).

---

## 📑 5. Szczegółowy podział nagłówków (Headers)

Nagłówki to pary `Klucz: Wartość`, które definiują parametry komunikacji.

A. General Headers (Ogólne)

Używane w zapytaniach i odpowiedziach, nie dotyczą samej treści (body).

- `Connection`: Zarządza połączeniem (np. `keep-alive`, `close`).
- `Date`: Data i godzina wysłania wiadomości.

B. Request Headers (Nagłówki żądania)

Wysyłane przez klienta, by sprecyzować kontekst zapytania.

- `Host`: **Wymagany w HTTP/1.1**. Określa domenę (np. `inlanefreight.com`). Pozwala na hosting wielu stron na jednym IP.
- `User-Agent`: Informacja o przeglądarce/narzędziu (np. `curl/7.65.3` lub `Mozilla/5.0`). Często zmieniany podczas testów, by ominąć blokady.
- `Accept`: Jakie typy treści klient potrafi obsłużyć (np. `text/html`, `application/json`).
- `Cookie`: Przesyła ciasteczka sesyjne do serwera (kluczowe dla utrzymania sesji).

C. Response Headers (Nagłówki odpowiedzi)

Wysyłane przez serwer, by przekazać informacje o sobie i zasobie.

- `Server`: Informacja o oprogramowaniu serwera (np. `Apache/2.4.41 (Ubuntu)`). **Cel rekonesansu!**
- `Set-Cookie`: Serwer nakazuje przeglądarce zapisać ciasteczko (np. `PHPSESSID`).
- `Content-Type`: Mówi klientowi, jak interpretować dane (np. `text/html; charset=UTF-8`).
- `Location`: Używany przy przekierowaniach (np. kod 301/302), wskazuje nowy URL.

D. Security Headers (Nagłówki bezpieczeństwa)

Kluczowe z punktu widzenia Pentestera – ograniczają ataki.

- `Strict-Transport-Security (HSTS)`: Wymusza połączenie przez HTTPS.
- `Content-Security-Policy (CSP)`: Zapobiega atakom XSS, określając, skąd mogą pochodzić skrypty.
- `X-Frame-Options`: Chroni przed atakami _Clickjacking_.

---

## 🛠️ Triki w cURL dla Nagłówków

Podczas labów HTB często będziesz musiał manipulować nagłówkami:


```
# Dodanie własnego nagłówka (np. zmiana User-Agent
curl -H "User-Agent: MyCustomScanner" http://target.com

# Przesłanie ciasteczka sesyjnego
curl -b "PHPSESSID=m4u64rql..." http://target.com

# Pobranie samych nagłówków odpowiedzi (bez body)
curl -I http://target.com
```

---

## 📑 **6. Metody HTTP (Verbs)**

Metody określają rodzaj akcji do wykonania na zasobie.

|Metoda|Opis|Zastosowanie / Ryzyko|
|---|---|---|
|**GET**|Pobiera zasób. Dane widoczne w URL (**Query String**).|Najczęstsza metoda pobierania stron.|
|**POST**|Wysyła dane w **Body** (np. formularze, pliki).|Logowanie, upload plików.|
|**HEAD**|Jak GET, ale zwraca same nagłówki (bez Body).|Szybki rekonesans wersji serwera.|
|**PUT**|Tworzy/nadpisuje zasób pod daną ścieżką.|**Ryzyko:** Możliwość wgrania webshella.|
|**DELETE**|Usuwa wskazany zasób.|**Ryzyko:** Denial of Service (DoS).|
|**OPTIONS**|Zwraca listę metod wspieranych przez serwer.|Sprawdzanie, czy aktywne są niebezpieczne metody.|

---

## 🚦 **7. Kody Statusu (Status Codes)**

Informują o wyniku zapytania. Kluczowe dla automatyzacji testów.

- **1xx** (Informacyjne): Zapytanie odebrane, proces trwa.
- **2xx** (Sukces): Np. `200 OK` – sukces, dane przesłane.
- **3xx** (Przekierowanie): Np. `301/302` – zasób przeniósł się pod inny URL (`Location`).
- **4xx** (Błąd Klienta):
    - `400 Bad Request` (błędna składnia),
    - `403 Forbidden` (brak uprawnień/blokada),
    - `404 Not Found` (brak zasobu).
- **5xx** (Błąd Serwera): Np. `500 Internal Server Error` – awaria aplikacji (często celowo wywoływana w testach).

---

## 🔐 **8. HTTP Basic Authentication**

Prosty mechanizm uwierzytelniania obsługiwany przez serwer (np. Apache/Nginx).

- **Działanie:** Serwer wysyła `401 Unauthorized` z nagłówkiem `WWW-Authenticate`.
- **Format:** Poświadczenia są przesyłane w nagłówku `Authorization` jako `login:hasło` zakodowane w **Base64**.
- **Uwaga:** To nie jest szyfrowanie! Base64 można łatwo odkodować. Bez HTTPS poświadczenia lecą czystym tekstem.

**Praktyka w cURL:**

bash

```
# Logowanie za pomocą flagi -u
curl -u admin:admin http://target.com

# Logowanie bezpośrednio w URL
curl http://target.com

# Podgląd nagłówka Authorization (Base64)
curl -v -u admin:admin http://target.com
```

---

## 🛠️ **9. Narzędzia Analizy (DevTools)**

Kluczowe zakładki dla Pentestera (F12 / Ctrl+Shift+I):

- **Network Tab:** Podgląd każdego zapytania, jego metody, kodu statusu i czasu ładowania.
- **Payload/Parameters:** Rozbicie skomplikowanych Query Strings (`?id=1&cat=test`) na czytelne pary klucz-wartość.
- **Response -> Raw:** Podgląd czystego kodu odpowiedzi serwera przed wyrenderowaniem przez przeglądarkę.

---

## 📮 10. Metoda POST

W przeciwieństwie do GET, parametry w POST przesyłane są w **Body** (ciele) zapytania, a nie w adresie URL.

**Zalety POST:**

- **Brak logowania:** Dane nie pojawiają się w logach serwera (w przeciwieństwie do parametrów URL).
    
- **Dowolny format:** Możliwość przesyłania danych binarnych (pliki) i obiektów JSON.
    
- **Brak limitów:** Adresy URL są ograniczone (zazwyczaj do ok. 2000 znaków), Body nie ma takich limitów.
    

### Praca z formularzami (Form Data)

Jeśli strona używa standardowego formularza logowania (username/password):

Bash

```
# Przesłanie danych formularza (-d)
curl -X POST -d 'username=admin&password=admin' http://target.com/login.php
```

### Praca z JSON

W nowoczesnych aplikacjach i API dane często lecą jako JSON. Wymaga to nagłówka `Content-Type`.

Bash

```
# Wysłanie JSONa (-H dla nagłówka i -d dla danych)
curl -X POST -d '{"search":"london"}' -H 'Content-Type: application/json' http://target.com/search.php
```

---

## 🍪 11. Uwierzytelnianie Sesyjne (Cookies)

Po poprawnym zalogowaniu serwer przesyła ciasteczko sesyjne (np. `PHPSESSID`). Musisz je dołączać do kolejnych zapytań, aby serwer wiedział, że jesteś zalogowany.

**Jak wyciągnąć i użyć ciasteczka:**

1. **Podejrzenie nagłówka odpowiedzi:** Użyj `-i`, aby zobaczyć `Set-Cookie`.
    
2. **Użycie w cURL:**
    
    Bash
    
    ```
    # Użycie ciasteczka przez flagę -b (cookie)
    curl -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://target.com/dashboard.php
    
    # Można też przez nagłówek -H
    curl -H 'Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://target.com/dashboard.php
    ```
    

---

## 🧬 12. Interakcja z CRUD API

API typu CRUD (Create, Read, Update, Delete) mapuje operacje na konkretne metody HTTP.

|Operacja|Metoda|Opis|
|---|---|---|
|**C**reate|**POST**|Dodaje nowy wpis do bazy.|
|**R**ead|**GET**|Pobiera dane (np. `/api/city/london`).|
|**U**pdate|**PUT / PATCH**|Aktualizuje istniejący wpis.|
|**D**elete|**DELETE**|Usuwa wpis.|

### Praktyczne przykłady cURL dla API:

#### 1. Odczyt (Read) + formatowanie JSON

Aby wynik był czytelny w terminalu, używamy narzędzia `jq`.

Bash

```
curl -s http://target.com/api.php/city/london | jq
```

#### 2. Tworzenie (Create)

Zazwyczaj wymaga metody POST i formatu JSON.

Bash

```
curl -X POST http://target.com/api.php/city/ -d '{"city_name":"HTB_City", "country_name":"HTB"}' -H 'Content-Type: application/json'
```

#### 3. Aktualizacja (Update)

Używamy **PUT**, wskazując konkretny zasób w adresie URL.

Bash

```
curl -X PUT http://target.com/api.php/city/london -d '{"city_name":"New_London"}' -H 'Content-Type: application/json'
```

> **Różnica PUT vs PATCH:** `PUT` zazwyczaj nadpisuje cały obiekt, `PATCH` służy do modyfikacji tylko wybranego pola.

#### 4. Usuwanie (Delete)

Bash

```
curl -X DELETE http://target.com/api.php/city/New_London
```

---

## 🛠️ Triki DevTools (F12)

- **Network Tab -> Copy as cURL:** Najszybszy sposób na zduplikowanie skomplikowanego zapytania z przeglądarki do terminala.

- **Storage Tab -> Cookies:** Możesz ręcznie wkleić skradzione/wygenerowane ciasteczko `PHPSESSID`, aby przejąć sesję w przeglądarce bez podawania hasła.

- **Console -> Fetch:** Możesz modyfikować zapytania "w locie" używając JavaScriptu bezpośrednio w przeglądarce.