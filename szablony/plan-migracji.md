# Szablon: plan migracji danych do Odoo ERP

Wersja Odoo / środowisko: `[wersja/hosting]` · Data migracji: `[data]` · Właściciel danych: `[rola]` · Migrator: `[rola]`

## 1. Zakres i odpowiedzialność

| Zbiór | Źródło | Rekordów szac. | Właściciel | Docelowy model/obszar | Czy w fazie 1? |
|---|---|---:|---|---|---|
| Kontrahenci | `[system]` | `[liczba]` | `[rola]` | `[Odoo]` | `Tak / Nie` |
| Produkty | `[system]` | `[liczba]` | `[rola]` | `[Odoo]` | `Tak / Nie` |
| Otwarte zamówienia | `[system]` | `[liczba]` | `[rola]` | `[Odoo]` | `Tak / Nie` |
| Zapasy i partie | `[system]` | `[liczba]` | `[rola]` | `[Odoo]` | `Tak / Nie` |
| Salda/faktury | `[system]` | `[liczba]` | `[rola księgowa]` | `[Odoo]` | `Tak / Nie` |

## 2. Mapowanie

Wersja mapowania: `[v]` · Źródło prawdy: `[system per obiekt]`

| Pole źródła | Cel w Odoo | Transformacja | Puste/duplikaty | Reguła walidacji | Właściciel |
|---|---|---|---|---|---|
| `[pole]` | `[pole]` | `[reguła]` | `[reguła]` | `[test]` | `[rola]` |

## 3. Próby (dry-run)

| Próba | Data | Ilość źr. | Ilość w Odoo | Różnica | Wynik uzgodnienia | Akceptujący |
|---|---|---:|---:|---:|---|---|
| DR-01 | `[data]` | `[n]` | `[n]` | `[n]` | `PASS/FAIL` | `[rola]` |
| DR-02 | `[data]` | `[n]` | `[n]` | `[n]` | `PASS/FAIL` | `[rola]` |

Dodatkowe uzgodnienia: salda, waluty, wycena, relacje, załączniki, partie, unikalność, statusy, test procesu po imporcie.

## 4. Cutover i delta

| Czas | Czynność | Właściciel | Warunek sukcesu | Plan awaryjny |
|---|---|---|---|---|
| T-1 | `[freeze / backup / kontrola]` | `[rola]` | `[dowód]` | `[co jeśli fail]` |
| T0 | `[delta + import + uzgodnienie]` | `[rola]` | `[dowód]` | `[co jeśli fail]` |
| T+1 | `[rekonsyliacja / smoke]` | `[rola]` | `[dowód]` | `[co jeśli fail]` |

Punkt bezpiecznego odwrotu: `[kiedy / kto decyduje]`

Plan obsługi transakcji po punkcie odwrotu: `[rekonsyliacja/kompensacja]`

## 5. Bramka

- [ ] Każdy zbiór ma właściciela i zatwierdzone mapowanie.
- [ ] Powtórzenie migracji nie dubluje danych.
- [ ] Uzgodniono liczność, wartości, salda i relacje.
- [ ] Krytyczne procesy zostały przetestowane na zmigrowanych danych.
- [ ] Backup i restore bazy/filestore są przetestowane.
- [ ] Gotowe freeze, delta, plan odwrotu i osoby decyzyjne.

Zatwierdzenie biznesowe: `[rola/data]` · Zatwierdzenie techniczne: `[rola/data]`

Instrukcja: [migracja danych](../docs/07-migracja-danych.md)
