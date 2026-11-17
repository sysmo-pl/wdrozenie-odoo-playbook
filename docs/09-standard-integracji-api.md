# Integracje Odoo API: standard projektowania, wdrożenia i utrzymania

Niezawodna integracja z Odoo zaczyna się od kontraktu: co przesyłamy, który system jest źródłem prawdy, jaki jest stabilny klucz i co robimy przy przekroczeniu czasu odpowiedzi, limicie lub błędzie danych. W Odoo 19 i nowszych nowe integracje powinny korzystać z JSON-2 API (jedno wywołanie = jedna transakcja); w Odoo 16–18 dostępne są tylko XML-RPC/JSON-RPC, które producent wycofuje (usunięcie zaplanowane w Odoo 22). Każda integracja potrzebuje idempotencji, kolejki błędów, monitoringu i raportu uzgodnienia.

Za integrację odpowiada architekt integracji razem z właścicielami danych po obu stronach.

## 0. Zanim wybierzesz API, opisz biznesową umowę między systemami

Każdy interfejs musi odpowiadać na osiem pytań:

1. Co: które obiekty i pola przenosimy oraz jak mapujemy jednostki, waluty, podatki i statusy?
2. Po co: jaki proces biznesowy jest odbiorcą danych i co się wydarzy bez dostawy?
3. Kto jest prawdą: który system może zmienić dane i kto rozstrzyga konflikt?
4. Kiedy: event/webhook/polling/manual; dopuszczalne opóźnienie i wolumen w szczycie?
5. Identyfikacja: stabilny klucz biznesowy, zewnętrzny ID, wersja zdarzenia, correlation ID?
6. Błędy: co robimy przy timeout, 429, 500, błędzie walidacji i konflikcie?
7. Bezpieczeństwo: uwierzytelnienie, uprawnienia, szyfrowanie, retencja, logi, właściciel klucza?
8. Nadzór: kto dostaje alert, czy można odtworzyć lub ręcznie ponowić synchronizację?

Zacznij od [karty integracji](../szablony/karta-integracji.md), nie od implementowania endpointu.

## 1. Wybór kanału komunikacji

| Wzorzec | Dobry, gdy | Główne ryzyko |
|---|---|---|
| Synchroniczne API | Użytkownik potrzebuje natychmiastowej odpowiedzi | Timeout i długie blokady; użytkownik nie wie, czy zapis się wykonał |
| Event/webhook | Zdarzenia powinny płynąć niemal natychmiast | Duplikaty, brak gwarancji kolejności i utrata zdarzeń przy awarii |
| Polling okresowy | Brak webhooków, prostsza rekonsyliacja | Opóźnienia, limity API, powtarzanie pobierania |
| Asynchroniczna kolejka/worker | Wiele zdarzeń, retry, niezawodność | Złożoność obsługi, obserwowalność i opóźnienie końcowe |
| Plik / EDI / batch | Partner używa stabilnego formatu wymiany | Spóźnione wykrycie błędu, duży batch i częściowe przetworzenie |

Nie projektuj wszystkiego „real-time”, jeśli biznes toleruje 15 minut, a dostawca API ma niskie limity. Dla operacji krytycznych często potrzeba asynchronicznego mechanizmu z ekranem statusu i raportem uzgodnienia.

## 2. Zewnętrzne API Odoo: co zależy od wersji

| Wersja | Zewnętrzne API | Co zaplanować |
|---|---|---|
| 16–18 | Tylko XML-RPC i JSON-RPC (`/xmlrpc/2`, `/jsonrpc`) | Przy aktualizacji do 19+ przejście na JSON-2 |
| 19–21 (21: wg zapowiedzi) | JSON-2 (`/json/2/<model>/<method>`) oraz wycofywane XML-RPC/JSON-RPC | Nowe integracje tylko na JSON-2 |
| 22 (planowana jesień 2028) | Usunięcie usług `common` i `object` XML-RPC/JSON-RPC (Odoo Online: 21.1) | Integracje na starym RPC przestaną działać |

- JSON-2 API (od Odoo 19) przyjmuje klucz API w nagłówku `Authorization` jako token typu bearer. Klucz tworzy się w preferencjach użytkownika (bezpieczeństwo konta) z obowiązkowym opisem i okresem ważności.
- Według dokumentacji Odoo 20.0 dostęp do danych przez zewnętrzne API mają tylko plany cenowe Custom; nie ma go w planach One App Free i Standard. Ograniczenie dotyczy planów subskrypcji Odoo: przy instalacji Enterprise sprawdź plan w umowie; instalacja własna Community nie ma planu cenowego.
- Operacje przez JSON-2 podlegają uprawnieniom użytkownika API (ACL, record rules, dostęp do pól). Stosuj odrębne konto techniczne o minimalnych uprawnieniach na daną integrację.
- Jedno wywołanie JSON-2 = odrębna transakcja SQL. Nie zakładaj, że trzy kolejne wywołania to jedna atomowa operacja; przy potrzebie atomowości zaprojektuj odpowiednią operację biznesową po stronie systemu, jeśli hosting/moduły na to pozwalają.
- Według dokumentacji Odoo 20.0 XML-RPC i JSON-RPC są przestarzałe: usługa `db` została usunięta w Odoo 20 (i Online 19.1), a usługi `common` i `object` mają zostać usunięte w Odoo 22 (jesień 2028) i Online 21.1. **Nie projektuj nowej integracji na starym RPC tylko dlatego, że przykład z internetu działa.**
- Klucze API mają okres ważności. Według dokumentacji Odoo 20.0 nie da się utworzyć klucza ważnego dłużej niż trzy miesiące: zaplanuj rotację co najwyżej kwartalną i test przełączenia przed wygaśnięciem.

Źródła: [Odoo 20: External JSON-2 API](https://www.odoo.com/documentation/20.0/developer/reference/external_api.html), [Odoo 20: External RPC API](https://www.odoo.com/documentation/20.0/developer/reference/external_rpc_api.html).

## 3. Kontrakt danych i mapowanie

Dokumentuj oddzielnie obiekt źródłowy, wersję schematu, mapę pól, walidacje, wartości opcjonalne, zestaw statusów, daty/czas i strefy, waluty/jednostki, a także reguły kasowania/anulowania. Nie wystarczy „wysyłamy JSON z zamówieniem”.

Dla statusów przygotuj tabelę: status źródłowy → status Odoo → warunek przejścia → dostępna kompensacja → właściciel decyzji. Wprowadź kontrakt kompatybilności wstecznej: jak wdrażamy nowe pole, usuwa się stare i ile czasu wspieramy dwie wersje.

Source of truth ustal per pole i proces, nie per cała aplikacja. Cena promocji może pochodzić ze sklepu, natomiast realizacja magazynowa z Odoo. Unikaj cyklu „A zmienia → B zmienia → A zmienia” bez zasady kierunku i konfliktów.

## 4. Idempotencja i duplikaty: warunek obowiązkowy

W praktyce retry i webhooki często dostarczają to samo zdarzenie więcej niż raz. Nie próbuj „zagwarantować exactly once” samą komunikacją sieciową. Zaprojektuj, by wiele dostaw miało jeden skutek biznesowy.

- Każdy rekord/komunikat powinien mieć stabilny `source_system + external_id + typ/wersja operacji` albo odrębny idempotency key.
- Przechowuj mapę identyfikatorów i status przetworzenia w kontrolowanym miejscu.
- Przy aktualizacji określ, kiedy `upsert` jest bezpieczny, a kiedy zmiana stanu wymaga odrębnej walidacji.
- Unikalność biznesową wymuszaj także na poziomie trwałego zapisu, gdy to stosowne: samo sprawdzenie w pamięci nie chroni przed równoczesnymi workerami.
- Po timeout po zapisie najpierw zweryfikuj wynik po kluczu/id, a dopiero później zdecyduj o ponowieniu.

Scenariusz testowy: integrator wysyła `ORDER-00017`, Odoo tworzy zamówienie, odpowiedź HTTP ginie. Kolejny request nie może utworzyć drugiego zamówienia ani podwójnego wydania. Kryterium: jeden dokument biznesowy, poprawny status, log wyjaśniający ponowienie.

## 5. Retry, błędy, DLQ i ręczna interwencja

| Sytuacja | Działanie | Nie rób |
|---|---|---|
| Timeout / błąd sieci | Sprawdź niepewny stan, ponów z tym samym kluczem, z backoffem | Twórz nowy dokument bez sprawdzenia wyniku |
| HTTP 429 / limit | Respektuj `Retry-After`, limituj współbieżność, backoff z jitter | Zasypuj API kolejnymi żądaniami |
| HTTP 5xx / awaria dostawcy | Kolejka, backoff, alert po progu, rekonsyliacja | Zgub zdarzenie po pierwszej porażce |
| Błąd danych / walidacji 4xx | Kolejka błędów, właściciel danych, korekta i ręczne ponowienie | Retry w pętli bez zmiany danych |
| Konflikt wersji/statusu | Rozstrzygnij według polityki source of truth | Nadpisuj nowsze dane starszymi |
| Częściowa operacja biznesowa | Kompensacja / uzgodnienie z biznesem | Uznawaj wszystkie kroki za jedną transakcję |

DLQ / kolejka błędów powinna umożliwiać operatorowi sprawdzenie: powodu, czasu, numeru zdarzenia, powiązanych rekordów, liczby prób i bezpiecznej procedury replay. Dostęp do payloadów ogranicz, jeśli zawierają dane osobowe.

## 6. Webhooki i wejście publiczne

Jeżeli system wystawia endpoint do przyjmowania zdarzeń, zweryfikuj podpis/HMAC lub inną metodę autentykacji przewidzianą przez nadawcę, ochronę przed replay, limit żądań, walidację schematu i rozmiaru, logikę autoryzacji oraz ochronę przed nadużyciami. Wyłącz techniczne komunikaty błędów dla anonimowego odbiorcy. Odbierz zdarzenie szybko, a ciężkie przetwarzanie przenieś do kontrolowanego mechanizmu kolejkowego, jeśli architektura tego wymaga.

**Nie zakładaj, że Odoo samo oferuje każdy potrzebny webhook lub kolejkowanie.** Mechanizm może wymagać dodatkowego komponentu lub prywatnego modułu; zweryfikuj zakres dla wersji i hostingu.

## 7. Monitoring i uzgadnianie danych

Monitoruj co najmniej:

- liczbę zdarzeń przyjętych, ukończonych, opóźnionych i nieudanych;
- wiek najstarszego zaległego zdarzenia oraz czasy realizacji end-to-end;
- powtarzające się błędy według systemu, obiektu, wersji i kategorii;
- duplikaty odfiltrowane przez idempotencję;
- rozbieżność liczności i sum między systemami w uzgodnionym oknie;
- wygasanie kluczy, certyfikatów i problem z autoryzacją;
- liczbę operacji kompensacyjnych i ręcznych replay.

Każdy przepływ ma `correlation_id`, który spina zdarzenie z rekordem w obu systemach, bez kopiowania do logów danych osobowych i tokenów. Alert ma wskazać, co operator powinien zrobić, a nie tylko „500”.

Rekonsyliacja nie jest funkcją opcjonalną. Przy przepływie zamówień i faktur porównuj np. liczbę dokumentów, sumy netto/brutto (zgodne reguły walutowe), identyfikatory zewnętrzne i statusy. Wyjaśniaj różnice wynikające z cutoff czasu i sytuacji w trakcie przetwarzania.

## 8. Bezpieczeństwo integracji

- Osobne konto bot/integration per system, minimalne role, brak współdzielonego konta admin.
- Klucze wyłącznie w menedżerze sekretów / bezpiecznej konfiguracji, nie w repozytorium, logach i dokumentach.
- Plan rotacji, odwołania i nadawania kluczy; osobne środowiska i zakresy dostępu.
- HTTPS/TLS, weryfikacja połączenia, ograniczenia adresów/egress zgodnie z architekturą.
- Minimalizacja przesyłanych danych, kontrola retencji, umowy i właściwe podstawy prawne, jeśli wymagane.
- Przegląd tego, jakie modele i pola są dostępne dla konta technicznego, w tym test dostępu do obcej spółki.
- Niezależny test odmowy dostępu, podszycia pod nadawcę, powtórzenia webhooka i nadmiernego payloadu.

## 9. Matryca testów awaryjnych integracji

| Test | Warunek | Oczekiwany wynik |
|---|---|---|
| TI-01 | Ten sam event dostarczony 3 razy | Jeden efekt biznesowy, historia ponowień |
| TI-02 | Timeout po zapisie w Odoo | Bez duplikatu po retry, stan wyjaśniony |
| TI-03 | Odpowiedź 429 przez 30 min | Kolejka zachowana, backoff, alert według progu |
| TI-04 | Błędny NIP lub jednostka | Odrzucony biznesowo rekord, brak „cichej naprawy” |
| TI-05 | Niezgodna wersja schematu | Bezpieczna odmowa + alert + umowa kompatybilności |
| TI-06 | Nieuprawniona spółka w żądaniu | Brak dostępu i brak skutku ubocznego |
| TI-07 | Awaria workerów na 2 godziny | Brak utraconych zdarzeń, odrobienie kolejki |
| TI-08 | Zmiana rekordu po obu stronach | Rozstrzygnięcie zgodnie z source of truth |
| TI-09 | Rotacja klucza API | Przełączenie bez utraty danych; stary klucz unieważniony |
| TI-10 | Fikcyjny zwrot / korekta zamówienia | Spójne, uzgadnialne dokumenty w obu systemach |

## 10. Definition of Done: integracja

- [ ] Jest karta integracji, schemat danych i właściciele po obu stronach.
- [ ] Uzgodniono source of truth, identyfikację i kierunki zmian.
- [ ] Zdefiniowano tolerowane opóźnienie, wolumen i sposób degradacji bez API.
- [ ] Testowane są duplikaty, timeout po zapisie, 429/5xx, walidacje i zmiany wersji.
- [ ] Zapewniono monitoring, alertowanie, replay i raport rekonsyliacji.
- [ ] Role kont technicznych, klucze i retencja spełniają security review.
- [ ] Uzgodniono procedurę awarii dostawcy i dyżury/eskalację.
- [ ] Dokumentacja integracji pozostaje w prywatnym repozytorium projektu; publicznie opisujemy wyłącznie wzorce.

Przypadki awarii: [scenariusze awarii integracji](21-incydenty-integracji-api.md) · [fikcyjny przypadek e-commerce po timeoutcie](../przyklady/sklep-zamowienia-timeout.md).
