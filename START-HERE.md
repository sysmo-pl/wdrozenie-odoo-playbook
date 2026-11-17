# Jak korzystać z playbooka: ścieżki według ról

Nie trzeba czytać wszystkiego. Wybierz rolę, przeczytaj dwa, trzy wskazane rozdziały, a szablony skopiuj do narzędzia, w którym prowadzisz projekt.

## Wybierz swoją ścieżkę

| Rola | Najpierw przeczytaj | Co powinno zostać po Twojej pracy |
|---|---|---|
| Sponsor / właściciel biznesowy | [01 Etapy](docs/01-etapy-wdrozenia.md), 15 Ryzyka *(wkrótce)* | Cele, budżet, granice zakresu, decyzje, właściciel odbioru |
| PM / delivery lead | [01 Etapy](docs/01-etapy-wdrozenia.md), [02 Kick-off](docs/02-kickoff-discovery.md), 12 Go-Live *(wkrótce)* | Plan, zależności, rejestr ryzyk i decyzji, bramki jakości |
| Konsultant / analityk | [03 Pytania](docs/03-pytania-do-klienta.md), [04 FIT/GAP](docs/04-mapa-procesow-fit-gap.md) | Procesy AS-IS/TO-BE, kryteria akceptacji, backlog i priorytety |
| Programista Odoo / lider techniczny (tech lead) | [05 Architektura](docs/05-architektura-srodowiska.md), [08 Python](docs/08-standard-modulow-python.md), [10 Security](docs/10-bezpieczenstwo.md) | Decyzje techniczne, testy, przegląd zmian, plan aktualizacji |
| Audytor istniejącego wdrożenia | 20 Audyt *(wkrótce)*, 18 Błędy *(wkrótce)* | Ocena ryzyka, lista dowodów i plan naprawczy |
| Integrator / DevOps | [09 API](docs/09-standard-integracji-api.md), [10 Security](docs/10-bezpieczenstwo.md), 13 Utrzymanie *(wkrótce)* | Kontrakt API, monitoring, retry, backup/restore, runbook |
| Key user / tester | 11 UAT *(wkrótce)*, 14 Szkolenia *(wkrótce)* | Scenariusze, dowody testów, podpisany odbiór biznesowy |

## Reguła pracy na każdym wymaganiu

1. Zapisz, kto ma potrzebę i po co (identyfikator w rodzaju `REQ-001`).
2. Poproś o konkretny przykład z życia firmy, nie tylko o opis idealnej funkcji.
3. Ustal, co uruchamia proces, kto decyduje, jakie są wyjątki i co go kończy.
4. Przypisz kryterium akceptacji, np. „magazynier bez roli księgowej nie może odczytać faktur”.
5. Pokaż standard Odoo i zdecyduj: proces / konfiguracja / Studio / moduł Python / integracja.
6. Zapisz właściciela decyzji, ograniczenia licencyjne i wpływ na aktualizacje oraz koszt utrzymania. Dopisz test pozytywny i negatywny oraz rolę, która go wykona.

## Co nie trafia do repozytorium

Tickety, nazwy baz, numery dokumentów, logi, sekrety, konfiguracja produkcyjna i kod klientów. Wypełnione szablony trzymaj w prywatnym repozytorium projektu. Instrukcje techniczne sprawdzaj dla konkretnej wersji Odoo, edycji i środowiska.

## Minimalny zestaw artefaktów na projekt

- [Karta projektu](szablony/karta-projektu.md): po kwalifikacji i na kick-off.
- [Notatka discovery](szablony/notatka-discovery.md) i [karta procesu](szablony/karta-procesu.md): po każdym warsztacie.
- [Rejestr wymagań FIT/GAP](szablony/rejestr-wymagan.md): przed zamrożeniem zakresu.
- [Macierz ról](szablony/macierz-rol.md): przed UAT.
- [Karta integracji](szablony/karta-integracji.md), [karta incydentu](szablony/karta-incydentu-integracji.md), [plan migracji](szablony/plan-migracji.md): przed developmentem i testami end-to-end.
- Scenariusze UAT *(wkrótce)*: przed odbiorem.
- Checklista Go-Live *(wkrótce)*: przed zgodą na uruchomienie.
- [Rejestr ryzyk i decyzji](szablony/rejestr-ryzyk-decyzji.md): prowadzony od dnia 1.
- [ADR](szablony/adr-decyzja-architektoniczna.md): dla decyzji o ważnych konsekwencjach technicznych i kosztowych.
- Karta audytu *(wkrótce)*: przy przejęciu utrzymania lub ocenie istniejącego Odoo.

## Co oznaczają poziomy priorytetu?

- P0 / blokujące start: problem z bezpieczeństwem, integralnością danych lub podstawowym procesem, którego nie da się bezpiecznie obejść.
- P1 / krytyczne dla biznesu: istotna luka z obejściem wymagającym pilnego działania.
- P2 / ważne: wpływa na efektywność, ale nie uniemożliwia bezpiecznej pracy.
- P3 / rozwój: usprawnienie poza niezbędnym zakresem uruchomienia.

Klasyfikację uzgadnia się z klientem. Priorytet w projekcie to co innego niż priorytet zgłoszenia w utrzymaniu.

Gdy coś już się zepsuło, zacznij od katalogu błędów *(wkrótce)*, a przy integracjach od [scenariuszy awarii](docs/21-incydenty-integracji-api.md).
