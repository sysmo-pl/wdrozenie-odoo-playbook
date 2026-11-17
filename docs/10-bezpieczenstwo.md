# Bezpieczeństwo wdrożenia Odoo ERP

Bezpieczeństwo wdrożenia Odoo opiera się na zasadzie minimalnych uprawnień: role biznesowe, prawa do modeli (ACL) i reguły rekordów (record rules) testowane na kontach rzeczywistych ról, a nie na administratorze. Do tego dochodzą izolacja spółek, osobne konta techniczne dla integracji, sekrety poza repozytorium, odseparowane środowiska i kopie zapasowe z przetestowanym odtworzeniem.

Zakres: role i uprawnienia, aplikacja, infrastruktura, integracje i ochrona danych. Właściciele: osoba odpowiedzialna za bezpieczeństwo klienta, tech lead, administrator oraz właściciel procesu. Dokument stanowi checklistę projektową, nie certyfikację bezpieczeństwa ani poradę prawną.

## 1. Najpierw model zagrożeń

Zanim nadamy dostępy, odpowiedz:

- Co jest wrażliwe: dane pracowników, klientów, faktur, wyceny, cenniki, sekrety API, dokumenty?
- Kto może próbować zobaczyć lub zmienić dane bez upoważnienia: inny dział, inna spółka, klient portalu, utracone konto, integracja?
- Jakie skutki biznesowe ma błąd: wyciek, fałszywa faktura, podwójny zwrot, brak możliwości odtworzenia?
- Jakie są ścieżki wejścia: UI, import, API, kontroler publiczny, eksport, raport, załączniki, backup?
- Kto jest właścicielem akceptacji ryzyka i jak często przegląda uprawnienia?

## 2. Minimalne uprawnienia

1. Zdefiniuj role biznesowe na procesach, nie przez kopiowanie konta administratora.
2. Dla każdego modelu ustal, kto może read / create / write / unlink oraz czy uprawnienia ograniczają się do firmy, magazynu lub zespołu.
3. Odoo stosuje ACL i record rules; reguły globalne i grupowe nie łączą się tak samo. Weryfikuj rzeczywisty efekt przy wielu grupach naraz.
4. Nie zakładaj, że ukrycie zakładki, przycisku czy domyślnego filtra blokuje dostęp przez API, eksport lub bezpośredni adres.
5. Pola wrażliwe mogą wymagać kontroli na poziomie pola i walidacji w metodach backendowych.
6. Zabroń zbędnego `sudo()` i nieautoryzowanych metod publicznych, które mogą ujawniać rekordy.
7. Testuj na rzeczywistych rolach użytkowników, nie jako `admin`.

Przykładowe negatywne testy: handlowiec z firmy A próbuje odczytać zamówienie firmy B; magazynier próbuje zmienić status faktury; klient portalu próbuje pobrać załącznik innego klienta; bot integracji próbuje listować dane HR.

## 3. Multi-company, portal, raporty

- Dla pól i dokumentów określ, czy dane są współdzielone, czy izolowane między firmami.
- Sprawdź kontekst `company_id`, dozwolonych spółek i rekordów powiązanych; błędne relacje często ujawniają dane nawet przy poprawnym głównym widoku.
- Zadbaj o reguły załączników, wiadomości/chatter, eksportów, szablonów e-mail i raportów PDF.
- Użytkownik portalu lub publiczny nie powinien mieć możliwości uzyskania rekordów przez zgadywanie identyfikatorów.
- Wykonaj testy macierzy uprawnień przed UAT i po znaczących zmianach modeli.

## 4. Tożsamość i dostęp administracyjny

- Konta indywidualne dla ludzi; konta bot dla integracji, bez współdzielenia hasła administratora.
- MFA/SSO, polityka haseł i lifecycle kont, zgodnie z możliwościami wdrożenia i standardami klienta.
- Minimalna liczba administratorów; udokumentowana procedura awaryjnego dostępu (break glass) z audytem.
- Szybkie odebranie dostępu przy odejściu pracownika/zmianie roli, w tym dostępów do hostingu i sekretów.
- Rozdzielenie uprawnień do zatwierdzenia, wykonania i księgowania, jeśli proces tego wymaga.
- Regularny przegląd ról: użytkownicy nieaktywni, uprawnienia odziedziczone, nadmiarowe integracje.

## 5. Sekrety i zewnętrzne API

- Nigdy nie umieszczaj tokenów, haseł, certyfikatów, kluczy prywatnych ani connection stringów w publicznym lub prywatnym kodzie repozytorium.
- Przechowuj sekrety w narzędziu do sekretów lub bezpiecznych ustawieniach poza repozytorium; ogranicz odczyt do potrzebnych usług.
- Używaj oddzielnych kluczy i kont dla każdego środowiska oraz integracji.
- Rotuj klucze zgodnie z dokumentacją konkretnej wersji; według dokumentacji Odoo 20.0 klucza API nie da się utworzyć na dłużej niż trzy miesiące, więc zaplanuj rotację. Testuj bezprzerwowe przełączenie i odwołanie starego klucza.
- Integracje muszą mieć minimalne role Odoo i testy negatywne dostępu.
- Loguj identyfikatory techniczne i kategorie błędów, nie payloady z danymi osobowymi, hasłami i tokenami.

JSON-2 API (Odoo 19 i nowsze) uwierzytelnia kluczem API z okresem ważności i działa w granicach uprawnień użytkownika, do którego należy klucz. W Odoo 16–18 integracje korzystają z XML-RPC/JSON-RPC: tu również stosuj osobne konto techniczne i klucz API zamiast hasła. Szczegóły: [External JSON-2 API (20.0)](https://www.odoo.com/documentation/20.0/developer/reference/external_api.html).

## 6. Deployment i infrastruktura

- Włącz TLS/HTTPS i prawidłowe certyfikaty, ogranicz usługi wystawione do Internetu i dostęp sieciowy.
- Zabezpiecz zarządzanie bazami i konfigurację serwera Odoo: `list_db = False` (ukryty menedżer baz), `dbfilter` dopasowany do domeny, silne hasło główne `admin_passwd` (master password), `proxy_mode = True` za reverse proxy z TLS, tryb wieloprocesowy (`workers`) dobrany do obciążenia.
- Aktualizuj system, zależności, Odoo oraz moduły zewnętrzne z kontrolą regresji.
- Ogranicz egress do usług potrzebnych serwerowi; zapobiegaj dostępowi z Odoo do nieautoryzowanych zasobów sieci wewnętrznej.
- Oddziel klientów i środowiska, jeśli hosting obsługuje więcej niż jeden projekt.
- Monitoruj błędy, logowania, statusy backupów, podejrzane żądania i zmianę konfiguracji.
- Przeprowadź twardnienie hostingu pod konkretny system i model zagrożeń, a nie tylko checklistę aplikacyjną.

[Zalecenia producenta dla deploymentu](https://www.odoo.com/documentation/20.0/administration/on_premise/deploy.html) należy zweryfikować względem używanej wersji.

## 7. Backup i możliwość odtworzenia

| Kontrola | Co musi być udowodnione |
|---|---|
| Zakres | Baza, filestore / załączniki i elementy konfiguracji niezbędne do uruchomienia |
| Izolacja | Kopie poza jednym punktem awarii, zabezpieczony dostęp |
| Retencja | Uzgodnione terminy zgodne z obowiązkami i biznesem |
| Odtwarzanie | Udana próba restore na odizolowanym środowisku |
| RPO/RTO | Formalnie zaakceptowana utrata danych i czas niedostępności |
| Odpowiedzialność | Osoba monitorująca backup, osoba decydująca o odtworzeniu |

Backup uznaje się za sprawdzony dopiero, gdy udało się odtworzyć kompletny działający proces i dostęp do załączników, a nie tylko pobrać plik archiwum.

## 8. Dane osobowe, prywatność i audyt

- Zbieraj najmniejszy potrzebny zestaw danych, określ cele i okresy przetwarzania oraz dostęp do każdej kategorii.
- Uzgodnij role administratora/podmiotu przetwarzającego, wymagane umowy, prawa osób i procedurę incydentową z właściwymi specjalistami.
- Zanonimizuj/zanuluj niepotrzebne dane przy kopiowaniu produkcji do staging i DEV.
- Kontroluj eksporty, automatyczne maile, załączniki, raporty, logi i kopie zapasowe jako pełnoprawne kanały ujawnienia danych.
- Ustal zasady retencji, usunięcia i ograniczenia dostępu zgodnie z obowiązkami prawno-księgowymi; nie kasuj dokumentów finansowych automatycznie bez weryfikacji.
- Nie obiecuj, że „Odoo jest zgodne z RODO” bez oceny konfiguracji, procesu i odpowiedzialności.

## 9. Scenariusze testów bezpieczeństwa

| ID | Rola / działanie | Oczekiwany wynik |
|---|---|---|
| SEC-UT-01 | Handlowiec A odczytuje fakturę spółki B | Brak dostępu; bez ujawnienia w eksporcie i API |
| SEC-UT-02 | Pracownik magazynu edytuje zaksięgowaną fakturę | Operacja zablokowana; zachowany ślad |
| SEC-UT-03 | Portalowy klient zgaduje ID cudzej faktury | Brak dostępu do dokumentu i załączników |
| SEC-UT-04 | Bot e-commerce odczytuje dane pracowników | Odmowa dostępu |
| SEC-UT-05 | Konto z dwiema grupami wykonuje niedozwoloną operację | Reguły działają zgodnie z matrycą |
| SEC-UT-06 | Wyłączono konto odchodzącego pracownika | Sesje i uprawnienia są odebrane zgodnie z procedurą |
| SEC-UT-07 | Wygasa lub jest odwołany klucz integracji | Dostęp odrzucony, powiadomienie, bez utraty danych |
| SEC-UT-08 | Kopia produkcji na stagingu | Wyłączone prawdziwe wysyłki i zminimalizowane dane |
| SEC-UT-09 | Restore z backupu | Dane i załączniki kompletne, wynik udokumentowany |
| SEC-UT-10 | Błąd API zawiera wrażliwy payload | Logi i odpowiedź nie ujawniają danych ani tokenów |

## 10. Bramka bezpieczeństwa (security gate) przed produkcją

- [ ] Zagrożenia i dane wrażliwe zidentyfikowane, właściciel ryzyka wyznaczony.
- [ ] Macierz ról zaakceptowana; testy pozytywne i negatywne przeszły.
- [ ] Multi-company, portal, załączniki, eksporty i raporty są objęte testami.
- [ ] Konto administracyjne i boty mają minimalne uprawnienia.
- [ ] Sekrety i klucze są poza repozytorium, istnieje proces rotacji.
- [ ] Środowiska są odizolowane, staging nie wysyła danych na produkcyjne endpointy.
- [ ] Kopia bazy + filestore została odtworzona testowo, RPO/RTO zatwierdzone.
- [ ] Logi, alerty, obsługa incydentu i lista kontaktów są gotowe.
- [ ] Ryzyka nieusunięte mają formalną decyzję oraz plan ograniczenia.

W praktyce: audyt *(wkrótce)* · przykład multi-company *(wkrótce)*.
