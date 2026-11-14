# Szablon: karta projektu Odoo ERP

> Użycie: skopiuj do prywatnego narzędzia projektu. Nie wypełniaj publicznie realnymi danymi klienta. Pola w nawiasach zastąp właściwymi danymi.

## 1. Metryka

| Pole | Wartość |
|---|---|
| ID projektu | `[PROJ-000]` |
| Nazwa robocza | `[anonimowa nazwa]` |
| Sponsor klienta | `[imię/rola (prywatnie)]` |
| Właściciel biznesowy / PM / tech lead | `[osoby]` |
| Wersja i edycja Odoo | `[wersja, edycja, plan]` |
| Hosting / region / odpowiedzialność | `[wariant]` |
| Data / wersja dokumentu | `[RRRR-MM-DD / v0.1]` |
| Status | `Discovery / Projekt / Realizacja / UAT / Go-Live / Hypercare` |

## 2. Cel biznesowy

Problem: `[co dziś nie działa]`

Wynik oczekiwany: `[jaki proces ma być możliwy]`

KPI bazowy i docelowy: `[metryka | dziś | po wdrożeniu | właściciel pomiaru]`

## 3. Zakres fazy 1

| W zakresie | Poza zakresem | Faza późniejsza |
|---|---|---|
| `[obszar + proces]` | `[jawne wyłączenie]` | `[usprawnienie]` |

Spółki/kraje/magazyny: `[zakres]`

Użytkownicy i role: `[liczność / role]`

Systemy zależne i integracje: `[nazwa ogólna / właściciel / rodzaj danych]`

Ograniczenia: `[prawne, licencja, hosting, dostępność, terminy]`

## 4. Decyzje i ryzyka

| ID | Treść / skutek | Właściciel | Termin | Status |
|---|---|---|---|---|
| `DEC-001` | `[decyzja]` | `[rola]` | `[data]` | `Otwarte` |
| `RISK-001` | `[ryzyko]` | `[rola]` | `[data]` | `Monitorowane` |

## 5. Warunki odbioru

- `[proces krytyczny]`: wykonany przez `[rola]` na stagingu z wynikiem `[kryterium]`.
- `[migracja]`: uzgodnienie danych z tolerancją `[próg, właściciel]`.
- `[integracja]`: retry, błędy i uzgodnienie z wynikiem `[kryterium]`.
- `[security]`: test ról/firm zakończony PASS.

## 6. Zatwierdzenie

Sponsor: `[osoba/data]` · Właściciel procesu: `[osoba/data]` · Tech lead: `[osoba/data]`

Otwarte sprawy: `[lista / kto / kiedy]`

Dalej: [etapy wdrożenia](../docs/01-etapy-wdrozenia.md)
