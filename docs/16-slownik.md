# Słownik pojęć: Odoo ERP i metodologia wdrożenia

Skróty i terminy z playbooku, razem z angielskimi nazwami, pod którymi występują w dokumentacji Odoo.

| Pojęcie | Znaczenie w tym playbooku |
|---|---|
| ACL | Access Control List: uprawnienia do operacji na modelu (np. odczyt/zapis/utworzenie/usunięcie). |
| ADR | Architecture Decision Record: uzasadniona decyzja architektoniczna z wariantami i konsekwencjami. |
| AS-IS | Obecny proces, z rzeczywistymi wyjątkami, systemami i odpowiedzialnością. |
| BOM | Bill of Materials: struktura materiałowa wyrobu dla MRP. |
| Bramka (gate) | Warunek przejścia do następnego etapu projektu. |
| Cutover | Operacyjne przełączenie systemów i danych, w tym freeze, delta i uzgodnienia. |
| DLQ (dead-letter queue) | Kolejka zdarzeń, których nie udało się przetworzyć; obsługiwana ręcznie. |
| DoD | Definition of Done: minimalny zestaw warunków uznania zadania za zakończone. |
| Dry-run | Próba importu/migracji/deploymentu przed produkcją z mierzalnym wynikiem. |
| E2E | End-to-end: cały proces od zdarzenia początkowego do wyniku biznesowego. |
| External ID | Stabilny identyfikator rekordu używany do odtworzenia relacji/integracji. |
| FIT/GAP | Analiza pokrycia wymagania przez standard oraz brakujących funkcji. |
| Go-Live | Rozpoczęcie pracy produkcyjnej na nowym systemie. |
| Hypercare | Okres wzmożonego wsparcia i monitoringu po starcie produkcyjnym. |
| Idempotencja | Powtórzenie operacji nie wywołuje kolejnego niepożądanego skutku biznesowego. |
| JSON-2 | Zewnętrzne API Odoo od wersji 19.0; jedno wywołanie = jedna transakcja; w subskrypcjach Odoo dostępne w planie Custom. |
| Key user (użytkownik kluczowy) | Reprezentant użytkowników odpowiedzialny za przykłady, testy i szkolenia. |
| KSeF | Krajowy System e-Faktur Ministerstwa Finansów; w Odoo 18+ obsługiwany przez moduł `l10n_pl_edi`. |
| l10n_pl_edi | Moduł polskiej lokalizacji Odoo (18+) do wysyłki faktur FA(3) do KSeF i pobierania faktur kosztowych. |
| MoSCoW | Must, Should, Could, Won't: sposób priorytetyzacji zakresu. |
| Multi-company | Obsługa wielu spółek wraz z regułami separacji/współdzielenia danych. |
| Odoo Online / Odoo.sh / on-premise | Odpowiednio: SaaS producenta (bez własnych modułów), platforma producenta z obsługą własnych modułów z Git, instalacja na własnym lub dzierżawionym serwerze. |
| Odoo Studio | Narzędzie edycji Enterprise do zmian pól, widoków i automatyzacji bez programowania. |
| ORM | Object-Relational Mapping: mechanizm pracy z rekordami i regułami modelu Odoo. |
| Record rules (reguły rekordów, `ir.rule`) | Ograniczenia dostępu do konkretnych rekordów na podstawie reguł i grup. |
| Retry / backoff | Ponowienie operacji po błędzie / rosnące odstępy między ponowieniami. |
| RPO | Maksymalna zaakceptowana utrata danych, mierzona w czasie. |
| RTO | Maksymalny zaakceptowany czas przywrócenia usługi. |
| Runbook | Scenariusz operacyjny krok po kroku, np. na dzień startu lub awarię. |
| SLA | Uzgodniony poziom usługi, np. godziny, czas reakcji, eskalacja. |
| Smoke test | Krótki test najważniejszych funkcji zaraz po wdrożeniu. |
| Source of truth (źródło prawdy) | System uprawniony do rozstrzygania prawidłowej wartości danego pola/rekordu. |
| `sudo()` | Metoda ORM uruchamiająca kod z pominięciem praw dostępu bieżącego użytkownika; wymaga uzasadnienia i testu. |
| TO-BE | Uzgodniony docelowy proces po zmianie organizacyjnej i wdrożeniu. |
| UAT | User Acceptance Testing: testy akceptacyjne wykonywane przez użytkowników biznesowych. |
| Upsert | Aktualizacja istniejącego rekordu albo utworzenie nowego według stabilnego klucza. |
| WMS | Warehouse Management System: system zarządzania magazynem. |

Zobacz też: [bank pytań](03-pytania-do-klienta.md) · integracje *(wkrótce)*
