# Szablon: rejestr ryzyk i decyzji projektu Odoo

> Prowadź w prywatnym narzędziu, aktualizuj podczas każdego statusu i przed każdą bramką jakości.

## 1. Ryzyka

| ID | Zdarzenie ryzykowne | Wpływ | Prawdopodobieństwo | Sygnał ostrzegawczy | Zapobieganie | Reakcja | Właściciel | Status |
|---|---|---:|---:|---|---|---|---|---|
| `RISK-001` | `[co może się stać]` | `1–5` | `1–5` | `[jak wykryć]` | `[działanie]` | `[fallback]` | `[rola]` | `Otwarte` |

Definicja poziomów: `[co oznacza wpływ 1–5, jakie progi eskalacji i kryteria NO-GO]`

## 2. Decyzje (Decision Log / ADR)

| ID | Decyzja / warianty | Dlaczego | Konsekwencje | Decydent | Data | Status |
|---|---|---|---|---|---|---|
| `DEC-001` | `[wariant A vs B]` | `[dowód]` | `[koszt/ryzyko/upgrade]` | `[rola]` | `[data]` | `Zaakceptowana` |

## 3. Change requests (CR)

| ID | Opis zmiany | Powód | Wpływ na termin | Wpływ na koszt | Wpływ na testy/security | Akceptacja |
|---|---|---|---|---|---|---|
| `CR-001` | `[co zmieniamy]` | `[powód]` | `[ocena]` | `[ocena]` | `[testy]` | `Tak/Nie + osoba/data` |

## 4. Reguły użycia

- Ryzyko nie jest zamknięte bez dowodu, np. test backup/restore.
- Decyzja jest jawna także wtedy, gdy wybrano wariant droższy lub z ograniczeniami.
- Zmiana zakresu jest zatwierdzana przed realizacją i przypisana do wymagań/UAT.
- Ryzyko z obszaru bezpieczeństwa i danych nie może zostać „zaakceptowane technicznie” bez właściwego właściciela biznesowego lub security.

Instrukcja: ryzyka i antywzorce *(wkrótce)*
