# Szablon: rejestr wymagań i FIT/GAP Odoo

Zasada: jedno wymaganie = jeden testowalny rezultat. Rozwiązania „przycisk”, „pole”, „raport” traktuj jako propozycje do oceny, nie definicję potrzeby.

| ID | Proces | Potrzeba biznesowa | Przykład / wyjątek | Rozwiązanie FIT/GAP | Priorytet | Właściciel | Kryterium UAT | Status |
|---|---|---|---|---|---|---|---|---|
| `REQ-001` | `PROC-001` | `[kto, co i po co]` | `[dowód: zanonimizowany dokument lub zrzut]` | `Standard / Konfiguracja / Studio / Python / API / Zmiana procesu` | `Must` | `[rola]` | `UAT-001` | `Do decyzji` |
| `REQ-002` | `PROC-002` | `[kto, co i po co]` | `[wyjątek]` | `Do analizy` | `Should` | `[rola]` | `UAT-002` | `Otwarte` |

## Karta szczegółowa dla pojedynczego REQ

ID / nazwa: `[REQ-XXX]` · Proces: `[PROC-...]` · Data: `[data]`

Jako `[rola]`, chcę `[czynność lub reguła]`, aby `[korzyść biznesowa]`.

Reguły biznesowe:

- `[warunek 1]`
- `[warunek 2]`
- `[wyjątek i co wtedy]`

Co jest zabronione: `[test negatywny]`

Reprezentatywny przykład: `[dane testowe, oczekiwany wynik]`

Wymogi danych/uprawnień/multi-company: `[opis]`

Warianty: `standard / konfiguracja / Studio / Python / API / zmiana procesu`

Wybrano, ponieważ: `[koszt, ryzyko, utrzymanie, upgrade, wynik demo]`

Akceptacja: `UAT-[...]`, wykonuje `[rola]`, oczekuje `[wynik]`.

Wymagane decyzje: `[DEC-...]` · Ryzyka: `[RISK-...]` · CR: `[CR-...]`

Właściciel / podpis: `[rola / data / status]`

## Gotowość wymagania do realizacji

- [ ] Właściciel i wartość biznesowa są znane.
- [ ] Reguła jest jednoznaczna, także dla wyjątków.
- [ ] Zademonstrowano standard Odoo w docelowej wersji i edycji.
- [ ] Zidentyfikowano dostęp do danych, wpływ na integracje i upgrade.
- [ ] Kryteria UAT są mierzalne.
- [ ] Koszt i zakres zaakceptowano; właściciel zatwierdził.

Metodyka: [FIT/GAP](../docs/04-mapa-procesow-fit-gap.md)
