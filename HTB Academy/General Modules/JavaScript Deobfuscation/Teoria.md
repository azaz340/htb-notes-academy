## 🌐 1. Analiza Source Code (Client-Side)

Współczesne aplikacje webowe opierają się na trzech filarach:

|Komponent|Rola|Lokalizacja|
|---|---|---|
|**HTML**|Struktura + inputy|`view-source`|
|**CSS**|Wygląd|`<style>` / `.css`|
|**JavaScript**|Logika (frontend)|`<script>` / `.js`|

---

### 🔍 Jak przeglądać kod?

- `CTRL + U` → View Source
- `F12` → DevTools (ważniejsze niż view-source)

> [!TIP]  
> Szukaj:
> 
> - komentarzy `<!-- -->`
> - ukrytych endpointów (`/api/`, `.php`)
> - dziwnych parametrów (`token`, `debug`, `dev`)

---

## 🎭 2. Obfuskacja Kodu (Code Obfuscation)

Kod celowo robiony tak, żeby:

- człowiek nie rozumiał
- przeglądarka działała normalnie

---

### 🎯 Po co to robią?

1. Ochrona kodu
2. Ukrycie logiki (pseudo-security)
3. Malware / unikanie detekcji

> [!WARNING]  
> Jeśli coś działa w przeglądarce → możesz to odtworzyć.  
> Obfuskacja to nie zabezpieczenie.

---

## 🛠️ 3. Techniki Obfuskacji

### A. Minifikacja

- usuwa spacje, nowe linie
- wszystko w jednej linii

➡️ pliki `.min.js`

---

### B. Packing

Kod „zapakowany” i odpakowywany przy wykonaniu

eval(function(p,a,c,k,e,d){...})

➡️ klasyk: **Dean Edwards Packer**

---

## 💻 4. Przykład

console.log('HTB JavaScript Deobfuscation Module');

➡️ po obfuskacji:

eval(function(p,a,c,k,e,d){...})

> [!TIP]  
> Stringi na końcu (`HTB`, `Module`) często zdradzają wszystko.

---

## 🧰 5. Narzędzia

|Narzędzie|Do czego|
|---|---|
|DevTools|debug|
|JSConsole|szybkie testy|
|Beautifier|formatowanie|
|UnPacker|rozpakowanie|

---

## ⚠️ 6. Kluczowa zasada

> **Client-side = publiczne**
xz
- API keys w JS → wyciek
- `checkPassword()` → do obejścia
- logika → do odtworzenia

---

## 🚀 7. Zaawansowana Obfuskacja

### Obfuscator.io

- Base64 / Hex stringi
- Control Flow Flattening

---

### Hardcore

- JSFuck
- JJEncode
- AAEncode

➡️ wygląda jak śmieci, działa jak normalny kod

---

## 🛠️ 8. Proces Deobfuskacji

### 1. Beautify

- `F12` → `{ }`
- lub Beautifier

---

### 2. Unpack

- UnPacker
- albo:

eval → console.log

---

### 3. Deobfuscate

- Deobfuscate.io
- JSNice

---

## 🔍 9. Analiza Kodu

function generateSerial() {  
  var xhr = new XMLHttpRequest();  
  xhr.open("POST", "/serial.php", true);  
  xhr.send(null);  
}

---

### 🔥 Na co patrzeć?

- `fetch()` / `XMLHttpRequest`
- endpointy (`.php`, `/api`)
- metody (`GET`, `POST`)
- parametry / headers

---

> [!TIP]  
> Dead code = złoto
> 
> Jak funkcja nie jest używana → uruchom ją ręcznie

---

## 🌐 10. HTTP Requests (cURL)

JS pokazał request → teraz robisz to ręcznie.

---

### GET

curl http://SERVER_IP:PORT/

---

### POST

curl -s http://SERVER_IP:PORT/ -X POST

---

### POST + dane

curl -s http://SERVER_IP:PORT/ -X POST -d "param=1"

---

### 🎯 HTB case

curl http://SERVER_IP:PORT/serial.php -X POST

➡️ dostajesz encoded output

---

> [!TIP]  
> JS to mapa.  
> Backend to skarb.

---

## 🔐 11. Encoding

Przykład:

ZG8gdGhlIGV4ZXJjaXNlLCBkb24ndCBjb3B5IGFuZCBwYXN0ZSA7KQo=

➡️ to nie hash → to encoding

---

## 🧠 Typy encodingu

### 🔵 Base64

✔ znaki: `A-Z a-z 0-9 + /`  
✔ końcówka: `=`

echo "text" | base64  
echo "encoded" | base64 -d

---

### 🟡 Hex

✔ tylko `0-9 a-f`

echo "text" | xxd -p  
echo "encoded" | xxd -p -r

---

### 🟢 ROT13

echo "text" | tr 'A-Za-z' 'N-ZA-Mn-za-m'

---

## 🛠️ 12. Flow (real exploit)

1. Request:

curl http://SERVER_IP:PORT/serial.php -X POST

2. Dostajesz encoded string
3. Rozpoznajesz → Base64
4. Decode:

echo "STRING" | base64 -d

5. Używasz:

curl http://SERVER_IP:PORT/serial.php -X POST -d "serial=WYNIK"

---

## ⚠️ 13. Encoding vs Encryption

- Encoding → odwracalne bez klucza
- Encryption → potrzebny klucz

➡️ 90% HTB = encoding

---

## 🚩 14. Szybki heurystyczny check

Jeśli string:

- ma `=`
- wygląda „czysto”
- tylko litery/cyfry

👉 **Base64**

---

## 💣 Najważniejsze (esencja)

1. JS pokazuje co robi backend
2. Ty robisz to ręcznie
3. Backend oddaje dane
4. Ty je dekodujesz
5. Wysyłasz poprawną odpowiedź

➡️ i masz flagę