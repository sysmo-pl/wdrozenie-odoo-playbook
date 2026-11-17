# Dedykowane moduły Odoo w Pythonie: wzorce i antywzorce architektoniczne

Dobry moduł Odoo nie tylko „działa na demo”. Ma izolowaną odpowiedzialność, testy uprawnień i danych, przewidywalne zachowanie w transakcji, kontrolę wydajności i plan aktualizacji.

Materiał do code review, projektowania i audytu. Szczegóły frameworka zależą od wersji; sprawdzaj [Odoo 20: developer docs](https://www.odoo.com/documentation/20.0/developer/tutorials.html) i [security](https://www.odoo.com/documentation/20.0/developer/reference/backend/security.html).

## Wzorce i antywzorce, które warto przeglądać

| Obszar | Zalecany wzorzec | Antywzorzec | Test lub dowód |
|---|---|---|---|
| Granica modułu | Jeden proces lub spójna funkcja; jasna zależność | Moduł „misc” zmieniający sprzedaż, HR, MRP naraz | Lista zależności i scenariuszy regresji |
| ORM | Korzystanie z mechanizmów ORM oraz operacji na zbiorach rekordów | Bezpośredni SQL omijający logikę uprawnień i ORM, gdy nie jest to niezbędne | Review przepływu danych i efektów ubocznych |
| Computed fields | Jawne zależności i dopasowanie przechowywania do zapytań | Ukryta operacja sieciowa w obliczeniu pola lub niejawne modyfikacje | Test ponownych obliczeń, wolumenów i błędów |
| `sudo()` | Wyjątkowy, uzasadniony odcinek z kontrolą danych wejściowych | Cały proces uruchamiany z podniesionymi uprawnieniami „bo inaczej nie działa” | Test użytkownika bez wymaganych praw |
| ACL / record rules | Kontrola modelu (`ir.model.access`), rekordów (`ir.rule`) i pól (`groups=` w definicji pola), także powiązanych | Pole/zakładka ukryte w UI traktowane jako zabezpieczenie | Negatywne testy UI, API i załączników |
| Wiele spółek | Jawne określenie spółki i reguł współdzielenia; `check_company=True` na relacjach i `_check_company_auto = True` | Operacje w domyślnej spółce z sesji programisty | Test A→B, B→A, konto z dostępem do dwóch spółek, wykonany przez `with_user()` |
| Walidacje | Walidacje biznesowe po stronie serwera i uzgodniony komunikat | Poleganie wyłącznie na JavaScript lub ręcznym procesie | Próba naruszenia reguły przez import/API |
| Transakcje | Jeden spójny skutek lub jawna kompensacja | „Po zapisie wyślij zewnętrzny request i licz na sukces” | Symulacja awarii przed i po zapisie |
| Kolejki / cron | Idempotentne zadania, obserwowalne stany i limity | Nieskończone ponawianie bez kontroli | Restart workera, dwa równoległe wykonania |
| Wydajność | Praca na partiach, profilowanie i kontrola operacji N+1 | Zapytanie/wywołanie per rekord bez pomiaru | Benchmark na realistycznej próbce |
| Testy | Jednostkowe, integracyjne, role, dane graniczne | Tylko „kliknięcie jako admin” na DEV | Raport z CI/testów i review |
| Aktualizacja wersji | Kompatybilność API i plan migracji modelu (`migrations/<wersja>/pre-migrate.py`, `post-migrate.py`) | Zależność od wewnętrznych szczegółów bez testu wersji | Próba aktualizacji na kopii neutralizowanej |

## Przykład PR do zatrzymania

Opis: „Dodano przycisk zatwierdzenia rabatu. Działa na adminie”. Ryzyka: brak rozdziału ról, brak backendowej walidacji, brak śladu decyzji, brak testów multi-company. Wynik review: `CHANGES REQUESTED`. Nie wystarczy zatwierdzić wygląd ekranu.

Minimalne poprawki: REQ z progiem rabatowym i wyjątkami; decyzja o grupie uprawnionej; walidacja serwerowa; test zwykłego handlowca, kierownika i użytkownika z innej spółki; weryfikacja w API/importach.

## Przykładowa karta kontroli modułu przed merge

- [ ] Powiązano zmianę z REQ i decyzją FIT/GAP.
- [ ] Podano wersję Odoo, edycję, hosting i zależności modułów.
- [ ] Właściciel modelu danych zatwierdził nowe pola, typy i migrację.
- [ ] Review obejmuje ACL, record rules, `sudo()`, kontrolery i ekspozycję danych.
- [ ] Testy działają na kontach o minimalnych uprawnieniach.
- [ ] Przetestowano wiele spółek, języków, walut, stref czasowych, jeśli dotyczy.
- [ ] Obsłużono równoległość i powtórzenia operacji krytycznych.
- [ ] Przetestowano błędne dane, utratę połączenia i rollback.
- [ ] Wydajność sprawdzono dla przewidywanego szczytu, a nie 10 rekordów.
- [ ] Zmiana ma plan wdrożenia, wycofania i scenariusz aktualizacji wersji.
- [ ] Nie umieszczono danych klientów, haseł ani tokenów w repozytorium/logach.

## Kiedy moduł nie jest odpowiednią odpowiedzią

- Wystarczy konfiguracja standardowego procesu.
- Reguła nie ma właściciela biznesowego albo nie uzgodniono wyjątków.
- Podstawowy problem dotyczy jakości danych lub chaosu organizacyjnego.
- Wymaganie jest tymczasowe, a koszt utrzymania po aktualizacji przewyższa korzyść.
- Hosting lub plan nie pozwala wdrożyć własnego modułu.

Powiązane: [pełny standard Python](08-standard-modulow-python.md), [drzewo decyzji](19-drzewo-decyzyjne-customizacja.md), [szablon zmiany modułu](../szablony/karta-zmiany-modulu.md), [ADR](24-decyzje-architektoniczne-adr.md).
