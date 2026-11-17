# Integracje Odoo API: scenariusze awarii, retry i uzgadniania danych

W integracji ERP trzeba odróżnić dostarczenie wiadomości od skutku biznesowego, który ma wystąpić dokładnie raz. Timeout nie dowodzi, że operacja się nie wykonała.

Zasady dotyczą projektowania integracji; konkretne endpointy, limity, autoryzację i transakcje potwierdź w dokumentacji używanej wersji Odoo: [Odoo 20: JSON-2 API](https://www.odoo.com/documentation/20.0/developer/reference/external_api.html). Zobacz też [standard integracji](09-standard-integracji-api.md).

## Reguła bazowa: stan operacji i dowody

Przykładowe stany pojedynczego zdarzenia: `RECEIVED → VALIDATED → PROCESSING → DONE` albo `RETRY_WAIT / REJECTED / MANUAL_REVIEW`. Loguj identyfikator zdarzenia, nazwę integracji, znacznik czasu, status, czas wykonania i kategorię błędu, a nie pełne dane klienta lub sekret.

Zanim włączysz retry, odpowiedz:

- Czy operacja jest bezpieczna do powtórzenia? Odczyt zwykle tak; utworzenie faktury/zamówienia wymaga ochrony przed powieleniem.
- Jaki jest stabilny identyfikator biznesowy po stronie źródła? `external_id` musi zachować znaczenie także po restarcie workerów.
- Gdzie utrwalasz status zdarzenia i odwzorowanie jego skutku w Odoo?
- Kto i w jakim czasie wykrywa zdarzenie nierozstrzygnięte?
- Jak uzgadniasz stan końcowy, jeśli potwierdzenie z sieci zaginęło?

## Scenariusz AWR-01: zamówienie utworzone, odpowiedź HTTP zaginęła

Przebieg: sklep wysyła zamówienie `SHOP-A/1009`, Odoo zapisuje dokument, klient HTTP otrzymuje timeout. Sklep ponawia wysyłkę. Ryzyko: dwa dokumenty, dwa wydania lub dwie faktury.

Kontrakt: unikalna kombinacja `(źródło, typ obiektu, external_id)` i trwałe powiązanie ze skutkiem biznesowym. Wymuszenie unikalności powinno być odporne na współbieżność i awarię procesu (sam odczyt „czy istnieje?” bez ochrony przed wyścigiem nie wystarczy). Jeśli odpowiedź zaginie, kolejne żądanie zwraca ten sam wynik albo trafia do bezpiecznej procedury uzgodnienia.

Diagnostyka: porównaj identyfikator źródła, dziennik wywołań, dokument w Odoo, status kolejki. Test akceptacji: pierwsze żądanie zostaje zapisane, odpowiedź celowo nie dociera, wykonujemy co najmniej trzy retry i nadal istnieje tylko jeden skutek biznesowy. Nie usuwaj dokumentów księgowych „dla porządku”.

## Scenariusz AWR-02: 429, 503 i przeciążenie kolejki

Przebieg: usługodawca czasowo limituje żądania; przychodzą kolejne zdarzenia. Ryzyko: lawina retry, opóźnienie kilku godzin i niekontrolowane żądania użytkowników.

Projekt: klasyfikacja błędów (`transient`, `validation`, `authorization`, `business_conflict`), kontrolowana liczba ponowień z backoff/jitter, obsługa `Retry-After` tam, gdzie jest udostępniane, limit równoległości i kolejka ręcznej obsługi dla zdarzeń nierozwiązywalnych. **Nie ponawiaj automatycznie błędnych danych ani błędu uprawnień** bez korekty przyczyny.

Test: przez 20 minut usługa zwraca tymczasowe błędy; po powrocie nie ma utraty ani duplikacji skutków, a zaległości stopniowo maleją. Metryka: wiek najstarszego zdarzenia i liczba `MANUAL_REVIEW` ważniejsze niż samo średnie 200 OK.

## Scenariusz AWR-03: statusy sklepu i Odoo wzajemnie się nadpisują

Przebieg: sklep wysyła `PAID`, Odoo publikuje `WAITING_PAYMENT` na podstawie opóźnionych danych; systemy zaczynają zmieniać status tam i z powrotem.

Projekt: tabela mapowania stanów, właściciel każdego pola, reguła chronologii i jawne przejścia niedozwolone. Rozdziel `payment_status` i `fulfilment_status`, jeśli reprezentują inne zdarzenia. Nie stosuj prostego „ostatnia modyfikacja wygrywa” przy działaniach nieodwracalnych.

Test: odwrócona kolejność dwóch zdarzeń, duplikat i opóźnienie 30 minut; żaden starszy status nie cofa poprawnie zakończonej operacji bez świadomej procedury korekty.

## Scenariusz AWR-04: wiele operacji, ale brak wspólnej transakcji

Przebieg: integracja wykonuje dwa oddzielne wywołania: rezerwuje zapas, a potem potwierdza zamówienie. Drugie wywołanie kończy się błędem. Ryzyko: częściowo wykonany proces.

Projekt: dla krytycznego, spójnego procesu preferuj jedną operację serwerową, jeśli architektura i dostępne metody to umożliwiają. Gdy granica przebiega między systemami, zaprojektuj proces wieloetapowy: status pośredni, kontrolę stanu, kompensację i ręczny wyjątek. Dwa kolejne wywołania JSON-2 w Odoo nie tworzą wspólnej transakcji SQL.

Test: przerwij drugi etap i sprawdź, czy wiadomo, co wykonano, czego nie wykonano, kto i jak przywraca spójność.

## Scenariusz AWR-05: synchronizacja przyrostowa pomija rekord

Przebieg: integracja pobiera „wszystko zmienione od ostatniej daty”. Dwóm rekordom przypisano ten sam znacznik czasu, ale limit strony powoduje pobranie tylko jednego. Ryzyko: rekord pominięty na zawsze.

Projekt: stabilne stronicowanie i uporządkowany kursor oparty na uzgodnionym zestawie pól (np. data modyfikacji + stabilny identyfikator), niewielkie nakładanie zakresów i deduplikacja. Dodatkowo okresowe pełne uzgodnienie liczników i identyfikatorów. Uwaga: znaczenie pól i możliwości filtrowania zależą od API.

Test: dwa rekordy z identycznym timestampem na granicy stron, zmiana rekordu podczas pobierania, restart synchronizacji w połowie przebiegu. Dowód: żadnego pominięcia, brak błędnej podwójnej zmiany.

## Scenariusz AWR-06: niedostępność KSeF lub systemu finansowego

Przebieg: dokument jest gotowy w ERP, ale system zewnętrzny nie odpowiada albo zwraca odmowę. Ryzyko: niejasne, czy dokument został skutecznie przyjęty; nieuprawnione powielenie albo niewłaściwa komunikacja z klientem.

Projekt: oddziel status przygotowania dokumentu w ERP od statusu jego obsługi w systemie zewnętrznym; zapisuj identyfikatory techniczne i stan potwierdzenia, jeśli interfejs je udostępnia. Uzgodnij z księgowością, kiedy i czy można ponowić operację oraz jakie potwierdzenie jest wystarczające. Prawne znaczenie statusów i trybów awaryjnych sprawdzaj w [oficjalnych materiałach KSeF](https://ksef.podatki.gov.pl/); konfigurację w Odoo i terminy obowiązku opisuje sekcja [KSeF w Odoo](06-konfiguracja-obszarow.md#6-ksef-w-odoo).

Test: odmowa walidacji, timeout, sukces z opóźnionym potwierdzeniem i restart workera; operator jednoznacznie odróżnia „wysłano”, „oczekuje”, „przyjęto”, „odrzucono” i „wymaga weryfikacji” według rzeczywistego kontraktu.

## Co musi zawierać karta integracji przed startem

| Pole | Wymagany dowód |
|---|---|
| Zdarzenie i źródło prawdy | Mapa pól/statusów zatwierdzona przez oba zespoły |
| Idempotencja | Powtórzenie po utracie odpowiedzi nie powoduje dubla |
| Błędy | Matryca błędów: retry / odrzucenie / manual review |
| Obserwowalność | Dashboard, alert, korelacja zdarzeń, retencja logów |
| Uzgodnienie | Wykaz rekordów brakujących/rozbieżnych i jego właściciel |
| Bezpieczeństwo | Konto bot z minimalnymi uprawnieniami, rotacja kluczy |
| Odbiór | Podpisane testy awaryjne i procedura odtworzenia |

Szablon: [karta incydentu](../szablony/karta-incydentu-integracji.md) · [karta integracji](../szablony/karta-integracji.md) · [bezpieczeństwo](10-bezpieczenstwo.md).
