# Kick-off i warsztaty discovery: instrukcja prowadzącego

Kick-off ustala zakres, role i sposób podejmowania decyzji. Warsztaty discovery mają pokazać, jak firma pracuje naprawdę: na konkretnych przykładach, z wyjątkami i wolumenami, oraz kto podejmuje decyzje.

## Co zebrać przed pierwszym spotkaniem

- Nazwiska/role decydentów, właścicieli procesów i key users (przechowuj prywatnie).
- Listę systemów, narzędzi, arkuszy i integracji, bez danych dostępowych.
- Zakres firm / krajów / spółek, magazynów, kanałów, głównych dokumentów.
- Przykładowy anonimowy cykl transakcji: zamówienie → realizacja → faktura → płatność.
- Skalę: użytkownicy, transakcje dzienne, SKU, faktury, zapotrzebowanie sezonowe.
- Termin graniczny, ograniczenia budżetowe, znane blokery i ryzyka.

## Agenda kick-off (90 minut, do dostosowania)

| Minuty | Prowadzący | Pytanie / rezultat |
|---|---|---|
| 0–10 | Sponsor | Po czym poznamy, że projekt się udał? |
| 10–25 | PM + klient | Co jest w zakresie, a co świadomie wykluczamy? |
| 25–40 | Właściciele procesów | Gdzie dziś tracimy najwięcej czasu / danych? |
| 40–55 | Konsultant | Które procesy pokażemy end-to-end i na jakich danych? |
| 55–70 | Architekt | Jakie systemy, wersje, ograniczenia prawne/IT i integracje mamy? |
| 70–85 | PM | Kto podejmuje decyzje, jak zatwierdzamy zmiany i ryzyka? |
| 85–90 | PM | Właściciele zadań na kolejne 7 dni; termin następnej sesji |

## Szablon warsztatu procesowego (60–120 minut)

1. Cel (5 min): „Dziś uzgadniamy, jak obsługujecie zwroty sprzedaży i co się musi stać w Odoo”.
2. Zakres (10 min): zdarzenie wejściowe, koniec procesu, role, systemy.
3. Pokaz AS-IS (25–40 min): użytkownik opowiada i odtwarza proces na fikcyjnych lub zanonimizowanych danych.
4. Wyjątki (20–30 min): anulacje, rabaty, wiele magazynów, błędy kuriera, korekty, uprawnienia.
5. Wolumeny i dane (15–20 min): liczby, wzorce identyfikacji, jakość, importy, integracje.
6. Podsumowanie (10–15 min): co uzgodniono, czego nie wiemy, właściciel i termin każdej odpowiedzi.

## 12 pytań, które prowadzący powinien zadać prawie zawsze

1. „Pokaż ostatni typowy przypadek, krok po kroku. Kto kliknął co i w jakim systemie?”
2. „Co dzieje się, jeśli zabraknie towaru, danych albo akceptacji?”
3. „Kto dziś może obejść regułę i kto widzi taką zmianę?”
4. „Czy istnieje więcej niż jedna ścieżka procesu? Jak często zdarzają się wyjątki?”
5. „Skąd bierzemy numer, datę i status dokumentu, który system jest prawdą?”
6. „Jak wygląda korekta lub cofnięcie operacji już zaksięgowanej albo wysłanej?”
7. „Których informacji nie mogą widzieć handlowcy, magazynierzy lub inne spółki?”
8. „Jak sprawdzacie, że w procesie nie zgubił się żaden dokument?”
9. „Jakie dane są obowiązkowe i kto odpowiada za ich uzupełnienie?”
10. „Ile takich operacji obsługujecie w zwykły dzień i w szczycie?”
11. „Co stanie się, jeśli integracja nie działa przez 4 godziny?”
12. „Co musi zobaczyć użytkownik, by powiedzieć: to działa?”

## Zasady dobrego protokołu

Zapisuj fakt, decyzję i hipotezę osobno. „Użytkownicy zawsze ręcznie zmieniają stawkę VAT” to sygnał do weryfikacji, nie reguła do automatycznego odtworzenia. Dla każdego otwartego pytania zapisz właściciela i datę. Nie przyjmuj sformułowania „zawsze” bez zapytania o kontrprzykład.

Po sesji wyślij podsumowanie do zatwierdzenia: proces, uzgodnione reguły, ryzyka, załączone artefakty, pytania otwarte, następna sesja. Wrażliwe notatki trzymaj wyłącznie w prywatnym narzędziu projektu.

## Kryteria wyjścia z discovery

- [ ] Każdy proces P0/P1 ma początek, koniec, właściciela i przynajmniej jeden reprezentatywny przykład.
- [ ] Ustalono scenariusze wyjątków, korekt i anulowań.
- [ ] Osobno zebrano wymagania biznesowe, pomysły na ekrany oraz wymogi prawne.
- [ ] Zidentyfikowano systemy źródłowe, wolumeny, dane i prawa dostępu.
- [ ] Otwarte decyzje mają właściciela oraz termin odpowiedzi.
- [ ] Powstał backlog wymagań do FIT/GAP, bez domniemanego „wszystko standard”.

Narzędzia: [pytania według obszarów](03-pytania-do-klienta.md) · [notatka discovery](../szablony/notatka-discovery.md) · [karta procesu](../szablony/karta-procesu.md)
