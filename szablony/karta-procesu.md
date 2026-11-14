# Szablon: karta procesu biznesowego AS-IS / TO-BE

ID: `[PROC-XXX]` · Nazwa: `[np. wydanie z magazynu]` · Właściciel: `[rola]` · Data / wersja: `[data/v]`

## 1. Zdarzenia i granice

- Wyzwalacz: `[co uruchamia proces]`
- Koniec: `[kiedy biznes uznaje proces za zakończony]`
- Wejścia: `[dokumenty/dane/uprawnienia]`
- Wyjścia: `[dokumenty/statusy/potwierdzenia]`
- Proces przed / po: `[ID i link]`
- KPI: `[miara/jednostka/obecna wartość/oczekiwana]`

## 2. AS-IS

| Nr | Rola | Działanie | System | Stan / dokument | Wyjątek |
|---|---|---|---|---|---|
| 1 | `[rola]` | `[czynność]` | `[system]` | `[status]` | `[wariant]` |
| 2 | `[rola]` | `[czynność]` | `[system]` | `[status]` | `[wariant]` |

Wąskie gardła / ręczne obejścia: `[opis z dowodem]`

## 3. TO-BE

| Nr | Rola | Docelowy krok | Odoo / integracja | Reguła walidacji | Test UAT |
|---|---|---|---|---|---|
| 1 | `[rola]` | `[czynność]` | `[system]` | `[warunek]` | `[UAT-...]` |
| 2 | `[rola]` | `[czynność]` | `[system]` | `[warunek]` | `[UAT-...]` |

## 4. Wyjątki i bezpieczeństwo

- `[wyjątek]` → `[reakcja i właściciel]`
- `[zabronione działanie]` → `[co użytkownik widzi i co blokuje backend]`
- `[błąd integracji]` → `[ponowienie/stan/alert]`
- `[korekta lub anulowanie]` → `[dozwolona procedura]`

## 5. FIT/GAP i akceptacja

Powiązane REQ: `[REQ-...]` · Wariant: `FIT / konfiguracja / Studio / Python / API / zmiana procesu`

Uzasadnienie wariantu: `[dlaczego ten wybór, alternatywy, wpływ na upgrade]`

Warunki akceptacji: `[3–5 mierzalnych zdań]`

Właściciel procesu zatwierdził: `[osoba/data]`

Dalej: [mapowanie FIT/GAP](../docs/04-mapa-procesow-fit-gap.md)
