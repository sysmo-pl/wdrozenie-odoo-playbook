# Dobre praktyki tworzenia dedykowanych modułów Odoo w Pythonie

Własny moduł Odoo w Pythonie powstaje dopiero wtedy, gdy standard, konfiguracja, Studio lub integracja nie rozwiązują potwierdzonej potrzeby, i tylko na hostingu, który na to pozwala (nie Odoo Online). Dobry moduł ma jedną odpowiedzialność, korzysta z ORM, ma przetestowane uprawnienia (ACL, reguły rekordów `ir.rule`, ograniczone `sudo()`), działa na wielu rekordach naraz i ma plan aktualizacji.

Nie ma tu kodu ani gotowych modułów, tylko zasady oceny i realizacji rozszerzeń. Interfejsy frameworka różnią się między wersjami, więc szczegóły sprawdzaj w dokumentacji swojej wersji Odoo.

## 0. Reguła podstawowa: nie pisz modułu, jeśli problem można rozwiązać prościej

Przed nowym modułem wypełnij [kartę zmiany](../szablony/karta-zmiany-modulu.md):

1. Jaką regułę biznesową realizujemy i na jakim procesie ją testujemy?
2. Co pokazał test standardu Odoo w docelowej wersji i edycji?
3. Czy da się osiągnąć ten sam wynik konfiguracją albo uzgodnioną zmianą procesu?
4. Czy dostępne jest Odoo Studio (wymaga edycji Enterprise; w cenniku Odoo jest w planie Custom albo w One App Free, jeśli Studio jest tą jedyną aplikacją; plan Standard go nie obejmuje, sprawdź aktualny cennik) i czy zakres zmiany mieści się w jego możliwościach?
5. Dlaczego potrzebny jest Python zamiast konfiguracji/Studio/integracji?
6. Czy klient zaakceptował koszt utrzymania i ponownego testowania po aktualizacjach?

**Nie startuj na Odoo Online z założeniem instalacji własnych modułów Python.** Zweryfikuj hosting i licencję zanim uznasz GAP za wykonalny.

## 1. Granice modułu i odpowiedzialność

Dobry moduł ma jedną czytelną odpowiedzialność biznesową, stabilny kontrakt z innymi modułami i niewiele zależności. Dokumentuj:

- `nazwa techniczna`, `właściciel`, `obsługiwane wersje Odoo`, `edycja`, `zależności` i `status wsparcia`.
- Co moduł dodaje, co nadpisuje, jakie modele/rekordy modyfikuje i jaki ma wpływ na istniejące procesy.
- Kto może wykonywać działania, jakie dane mogą wyjść na zewnątrz i gdzie pojawiają się efekty uboczne.
- Jak włączyć / wyłączyć funkcję bez uszkodzenia historycznych danych; jak wykonać aktualizację i test migracyjny. Zmiany modeli wymagające przekształcenia danych opisuj skryptami `migrations/<wersja>/pre-migrate.py` i `post-migrate.py`.

Nie twórz jednego modułu „wszystko_custom” zawierającego CRM, magazyn, faktury i integracje tylko dlatego, że jest szybciej. Zbyt drobne moduły również zwiększają złożoność: granicę wyznacza spójna odpowiedzialność.

## 2. Struktura i manifest (bez publikowania kodu)

W prywatnym repozytorium modułu utrzymuj konsekwentny podział: `models`, `security`, `views`, `data`, `report`, `wizard`, `controllers` (jeśli potrzebne), `tests`, `i18n`, a także manifest z opisem zależności i danych ładowanych podczas instalacji.

Review struktury:

- [ ] Manifest zawiera wyłącznie wymagane zależności, metadane i prawidłową kolejność zasobów.
- [ ] Moduł instaluje się na świeżej bazie docelowej wersji, a aktualizuje na reprezentatywnej kopii.
- [ ] Identyfikatory XML / external IDs są stabilne i nie kolidują z innymi dodatkami.
- [ ] Dane inicjalne i konfiguracja nie nadpisują bez potrzeby wartości produkcyjnych podczas aktualizacji.
- [ ] Uprawnienia i reguły danych są wersjonowane oraz testowane jak logika biznesowa.
- [ ] Teksty dla użytkownika są przygotowane do tłumaczeń, nie zaszyte jako przypadkowe ciągi.

Przy zmianie manifestu lub migracji sprawdź kompatybilność z aktualną wersją frameworka i dokumentacją jej mechanizmu upgrade.

## 3. ORM i model danych

1. Preferuj ORM Odoo do operacji na rekordach, walidacji dostępu i działania z mechanizmami frameworka. Surowy SQL wymaga szczególnej ostrożności: omija część mechanizmów ORM, uprawnień i cache. Jeśli jest konieczny, stosuj parametryzację, review i testy.
2. Modeluj relacje świadomie: po co jest Many2one/One2many/Many2many, jak działa usuwanie rekordu i czy informacja jest współdzielona między spółkami.
3. Pola wyliczane (computed fields): zdefiniuj zależności (`@api.depends`), obsługuj zbiór wielu rekordów (recordset) i oceniaj koszt pola zapisywanego (`store=True`) wobec wyliczanego na żądanie.
4. Onchange nie jest walidacją bezpieczeństwa. Użytkownik i integracja mogą nie przejść przez formularz. Reguły integralności powinny istnieć również po stronie ORM/bazy, odpowiednio do ograniczenia.
5. Zadania cykliczne: dokumentuj harmonogram, idempotencję, limit danych, obsługę błędu i zachowanie przy równoległym uruchomieniu.
6. Przejścia stanów: jedna spójna reguła biznesowa po stronie serwera, testy dla nielegalnego przejścia i ponownego uruchomienia.
7. **Nie przechowuj dwóch niezależnych kopii tej samej prawdy**, np. zapasu, gdy można z niego wyliczyć wynik; gdy denormalizacja jest celowa, opisz synchronizację i uzgadnianie.

### Pytania na review ORM

- Czy zmiana obsługuje wiele rekordów naraz (batch), czy ukradkiem zakłada pojedynczy `self`?
- Czy zapis powoduje nieoczekiwane rekursywne wywołania `create`/`write` lub automatyzacji?
- Czy wykonanie metody drugi raz daje poprawny wynik, czy tworzy duplikat?
- Czy obsłużono rekord nieistniejący, równoczesną edycję i konflikt unikalności?
- Czy błąd wycofuje transakcję, a informacja dla użytkownika jest zrozumiała i nie ujawnia sekretów?

## 4. Bezpieczeństwo: ACL, record rules, pola, kontrolery

**Nigdy nie uznawaj ukrycia przycisku za zabezpieczenie.** W Odoo dostęp kontrolują mechanizmy działające na serwerze i w bazie; UI jest tylko prezentacją.

- **Model ACL (`ir.model.access`):** jawnie określ operacje read/create/write/unlink dla grup. Uprawnienia bez przypisanej grupy mogą niezamierzenie poszerzyć dostęp; sprawdź rzeczywistą semantykę w wersji Odoo.
- **Record rules (`ir.rule`):** ograniczają rekordy dostępne dla danych ról; różne reguły grupowe i globalne łączą się w różny sposób. Testuj użytkowników z więcej niż jedną grupą.
- Dostęp do pól (field-level access): pola z informacjami płacowymi, finansowymi i wrażliwymi chroń serwerowo atrybutem `groups=` w definicji pola, a nie wyłącznie atrybutem widoku.
- **`sudo()`:** traktuj jako kontrolowane podwyższenie uprawnień. Każdy przypadek musi mieć powód, minimalny zakres, walidację wejścia i test, czy nie pozwala odczytać/zapisać cudzych danych.
- Wiele spółek (multi-company): weryfikuj `company_id`, zasady współdzielenia danych, kontekst firmy i niespójne relacje między spółkami; dla relacji używaj `check_company=True` i `_check_company_auto = True`. Nie polegaj na tym, że użytkownik widzi tylko wybrany filtr.
- Kontrolery i endpointy: definiuj uwierzytelnianie i autoryzację, walidację payloadu, limity, bezpieczne logowanie błędów, właściwy model CSRF i testy użytkownika niezalogowanego.
- Dostęp portalu i publiczny: testuj ręcznie i automatycznie; użytkownicy publiczni nie mogą odziedziczyć wygodnych uprawnień administracyjnych.

Negatywny test obowiązkowy: magazynier nie odczytuje faktury z innej spółki poprzez import/API/URL, nawet jeśli panel nie wyświetla odpowiedniego menu. W testach automatycznych wykonuj operacje jako konkretny użytkownik (`with_user()`), a nie jako superużytkownik. Testuj także użytkownika z dwiema rolami.

## 5. Wydajność i skalowanie

- Unikaj N+1: nie wykonuj wyszukiwania ani zapytania dla każdego rekordu w pętli, jeżeli można grupować operacje.
- Przetwarzaj w batchach: duże importy i synchronizacje dziel na porcje z checkpointami, unikaj bardzo długich transakcji.
- Pre-fetch / agregacja: korzystaj z mechanizmów frameworka, operacji zbiorczych i agregacji, gdy to uzasadnione.
- Indeksy: dodawaj tylko po potwierdzeniu problemu i ocenie kosztu zapisu; nie indeksuj każdego pola „na zapas”.
- Computed fields: sprawdź koszt ponownego przeliczenia przy zmianie zależnych rekordów.
- Załączniki: oceń filestore, rozmiar i retencję, nie przerzucaj dużych plików bez kontroli.
- Pomiar: wykonaj profilowanie na reprezentatywnej liczbie rekordów i wariancie szczytowym; zapisz wynik przed i po zmianie.

Przykładowy próg akceptacji (ustalany per projekt): tworzenie zamówienia z 50 pozycjami w określonym środowisku nie trwa dłużej niż uzgodnione X sekund; test dla kilku jednoczesnych użytkowników nie generuje utraty danych. Nie publikuj fikcyjnych „ogólnych limitów Odoo”.

## 6. Transakcje, błędy i prace w tle

- Wewnątrz operacji biznesowej dąż do atomowości: wszystkie zmiany kończą się sukcesem albo transakcja jest cofana.
- Nie wykonuj nieodwracalnych wywołań do świata zewnętrznego w miejscu, w którym rollback bazy może pozostawić odmienny stan w API. Użyj wzorca integracyjnego z identyfikatorem zdarzenia, idempotencją, outboxem/kolejką lub kontrolowaną kompensacją.
- **Nie rób ręcznych commitów „bo działa”.** Zaburzają semantykę transakcyjną frameworka i rollback, chyba że udokumentowany mechanizm i ekspert wyraźnie tego wymaga.
- Timeout i ponowienie to osobne scenariusze projektowe, nie tylko `try/except`.
- Błędy dla użytkownika mają wyjaśniać działanie, nie pokazywać stack trace ani tokenów.

## 7. Testy modułu: minimalna matryca

| Warstwa | Co sprawdzamy | Dowód |
|---|---|---|
| Logika Python/ORM | Reguły, ograniczenia (constraints), zależności pól, wiele rekordów naraz | Test automatyczny w środowisku Odoo |
| Uprawnienia | ACL, record rules, spółki, role, portal/public | Testy pozytywne i negatywne |
| Integracja UI | Widok, akcja, workflow, dostęp w przeglądarce | Test eksploracyjny lub test tour/E2E |
| Duże dane | Batch, czas, zasoby, indeksy | Raport z pomiaru |
| Aktualizacja | Instalacja od zera i upgrade z poprzedniej wersji modułu | Log i uzgodnienie danych |
| Integracja zewnętrzna | Retry, timeout, duplikaty, brak API | Test kontraktu / symulacja błędów |

Framework Odoo wspiera m.in. testy oparte o `TransactionCase` i `HttpCase` oraz testy JS/tours; dobierz je do wersji i typu funkcji. [Oficjalna dokumentacja testowania Odoo 20](https://www.odoo.com/documentation/20.0/developer/reference/backend/testing.html).

## 8. Standard code review (20 pytań)

Funkcjonalność

1. Czy `REQ` i akceptacja biznesowa są powiązane z tą zmianą?
2. Czy można wykonać cały scenariusz, a nie tylko zapisać formularz?
3. Czy obsłużono anulację, korektę, wielowalutowość i wartości puste, jeśli dotyczą?
4. Czy nie dublujemy reguły już dostępnej w standardzie lub innym module?
5. Czy walidacje działają poza interfejsem użytkownika?

Dane i bezpieczeństwo

6. Czy ACL i record rules realizują zasadę najmniejszych uprawnień?
7. Czy kod korzysta z `sudo()`? Dlaczego i z jakim testem negatywnym?
8. Czy poprawnie działa multi-company oraz domyślna spółka użytkownika?
9. Czy nie ma SQL injection, nieautoryzowanej serializacji ani niebezpiecznych danych wejściowych?
10. Czy logi, błędy, powiadomienia i załączniki nie ujawniają danych wrażliwych?

Działanie i utrzymanie

11. Czy unikaliśmy niepotrzebnego SQL i zapytań w pętli?
12. Czy operacje są bezpieczne przy ponowieniu i równoczesnym wywołaniu?
13. Czy zależności i external IDs są stabilne, a instalacja/upgrade testowane?
14. Czy usunięcie/dezaktywacja modułu nie psuje istniejących dokumentów bez planu?
15. Czy dodane pola/model danych mają przemyślaną retencję i wpływ na raporty?

Jakość

16. Czy testy automatyczne obejmują ścieżkę pozytywną, odmowę, wyjątek i duplikat?
17. Czy zmiana została sprawdzona na docelowej wersji Odoo i edycji?
18. Czy konfiguracja i parametry są udokumentowane bez sekretów?
19. Czy istnieje plan wydania/rollback/kompensacji i właściciel utrzymania?
20. Czy teksty widoczne dla użytkownika i tłumaczenia są zrozumiałe, a dokumentacja funkcji aktualna?

## 9. Definicja Done (DoD) dla modułu dedykowanego

- [ ] Potwierdzono GAP oraz decyzję „dlaczego Python, a nie standard”.
- [ ] Opisano modele, rekordy, role, scenariusze, integracje i ryzyko wielospółkowe.
- [ ] Instalacja/aktualizacja działa na docelowej wersji i edycji.
- [ ] Przeprowadzono code review, statyczną kontrolę jakości i testy automatyczne.
- [ ] UAT potwierdza proces oraz testy odmowy dostępu i wyjątków.
- [ ] Jest dokumentacja administratora, parametrów, monitoringu i procedury awarii.
- [ ] Jest decyzja o wpływie na przyszły upgrade Odoo, koszcie utrzymania i właścicielu.
- [ ] Żaden kod klienta ani konfiguracja produkcyjna nie trafia do publicznego repozytorium.

## 10. Antywzorce, które powinny zapalić czerwoną lampkę

| Antywzorzec | Co może się stać | Lepsza alternatywa |
|---|---|---|
| „Dajmy wszystkim admina, żeby workflow działał” | Ujawnienie lub zmiana danych | Poprawne grupy, ACL i record rules |
| `sudo()` na każdym wyszukiwaniu | Ominięcie kontroli dostępu | Minimum uprawnień, precyzyjna walidacja |
| Walidacja tylko przez UI/onchange | API/import pomija blokadę | Reguła po stronie ORM/bazy |
| Jeden gigantyczny custom module | Trudne upgrade'y i testy | Odpowiedzialności i jasne zależności |
| Raw SQL bez walidacji i review | Spójność/caching/access ryzyko | ORM; SQL wyjątkowo i parametryzowany |
| Brak testów wielospółkowości | Wyciek danych między firmami | Test różnych kontekstów firm i użytkowników |
| Synchronizacja API w transakcji bez idempotencji | Duble, niespójne dokumenty | Kolejka, outbox, kompensacja, monitoring |
| „Wdrożymy bez testowania upgrade'u” | Awaria po zmianie Odoo | Staging + test migracji + przegląd API |

## 11. Źródła techniczne

- [Odoo 20: Coding Guidelines](https://www.odoo.com/documentation/20.0/contributing/development/coding_guidelines.html)
- [Odoo 20: Security in Odoo](https://www.odoo.com/documentation/20.0/developer/reference/backend/security.html)
- [Odoo 20: Testing](https://www.odoo.com/documentation/20.0/developer/reference/backend/testing.html)
- [Odoo 20: Developer Tutorials](https://www.odoo.com/documentation/20.0/developer/tutorials.html)

Przykłady do code review: [wzorce i antywzorce Python](23-wzorce-antywzorce-python.md) · [drzewo decyzji customizacji](19-drzewo-decyzyjne-customizacja.md) · [ADR](24-decyzje-architektoniczne-adr.md).
