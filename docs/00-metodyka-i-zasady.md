# Zasady pracy przy wdrożeniu Odoo

Każde ustalenie w projekcie powinno skończyć się zmianą procesu, konfiguracją albo zadaniem programistycznym, z właścicielem, dowodem i kryterium odbioru. Projekt prowadzi się w stałym rytmie spotkań, z jednym odpowiedzialnym (Accountable) za każdą decyzję i z bramkami jakości między etapami. Etap jest zakończony dopiero wtedy, gdy istnieje zatwierdzony artefakt, a otwarte wyjątki mają właściciela i plan.

## Model działania: decyzja → dowód → odbiór

Każde ustalenie prowadzi do jednej z trzech rzeczy: zmiany procesu, konfiguracji Odoo lub pracy developerskiej. Nie rozpoczynamy pracy tylko na podstawie ustnej deklaracji „chcemy mieć tak jak w Excelu”. Oczekujemy konkretnego przypadku, odpowiedzialnego właściciela i mierzalnego wyniku.

### Stały rytm projektu: proponowany standard

| Częstotliwość | Spotkanie / aktywność | Wynik |
|---|---|---|
| Codziennie w aktywnym sprincie | Krótkie uzgodnienie blokad zespołu | Zaktualizowane zależności i właściciele działań |
| Raz w tygodniu | Status z liderem klienta | Decyzje, zmiany zakresu, ryzyka, budżet, termin |
| Co 1–2 tygodnie | Demo zakończonych procesów | Informacja zwrotna i zaakceptowane kryteria |
| Po każdej sesji discovery | Protokół warsztatu | Lista otwartych pytań z terminem odpowiedzi |
| Przed każdą bramką jakości | Przegląd bramki jakości | Go / no-go / warunkowe go z listą wyjątków |

Częstotliwość dopasowuje PM do skali i umowy. Otwarte decyzje nie mogą jednak zostawać bez właściciela.

## RACI: minimalne role

- Sponsor klienta: zatwierdza budżet, priorytety i decyzje o istotnym ryzyku.
- Właściciel procesu: odpowiada za rzeczywistą poprawność działania procesu i akceptację wyniku.
- Key user: pokazuje wyjątki, wykonuje testy UAT, przygotowuje szkolenie operacyjne.
- PM wdrożenia: harmonogram, zależności, rejestr decyzji, zmiany zakresu, komunikacja.
- Konsultant Odoo / analityk: mapowanie procesu, FIT/GAP, konfiguracja, kryteria akceptacji.
- Tech lead / developer: architektura rozszerzeń, jakość techniczna, testy i plan aktualizacji.
- Integrator / DevOps: komunikacja systemów, środowiska, obserwowalność, odtwarzanie i bezpieczeństwo infrastruktury.
- Właściciel danych po stronie klienta: jakość, poprawność i odbiór migracji.

Nie wpisuj „wszyscy” jako właściciela procesu. Każda decyzja ma jedną osobę ostatecznie odpowiedzialną (Accountable); współuczestników może być wielu.

## Artefakt to dowód, a nie dokument dla dokumentu

- Wymaganie: `REQ-XXX`, właściciel, cel, przykład, rozwiązanie, test, status.
- Proces: identyfikator `PROC-XXX`, AS-IS, TO-BE, wyjątki, zależności i metryka.
- Decyzja: `DEC-XXX`, warianty, rekomendacja, koszt konsekwencji, akceptujący.
- Ryzyko: `RISK-XXX`, prawdopodobieństwo, wpływ, działanie, sygnał alarmowy.
- Zmiana: `CR-XXX`, powód, efekt na zakres/czas/cenę, akceptacja przed wykonaniem.
- Incydent: `INC-XXX`, skutek, priorytet, obejście, właściciel, post-mortem.

## Kiedy eskalować?

Eskaluj od razu, gdy: (1) istnieje ryzyko utraty lub ujawnienia danych; (2) nie ma osoby zdolnej zatwierdzić reguł księgowych; (3) kluczowa integracja nie ma dostępu do dokumentacji/testów; (4) luka zakresu wywraca termin Go-Live; (5) istnieje brak możliwości przywrócenia kopii danych.

## Definicja zakończenia etapu

Nie wystarcza komunikat „gotowe”. Etap jest zakończony, gdy: istnieje artefakt, jest zatwierdzony przez właściciela, sprawdzono kryteria jakości, a pozostałe wyjątki mają jawny status i plan. Szczegóły w [etapach 0–10](01-etapy-wdrozenia.md).
