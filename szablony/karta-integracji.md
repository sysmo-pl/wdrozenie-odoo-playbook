# Szablon: karta integracji Odoo API

ID: `INT-[...]` · Nazwa: `[np. sklep ↔ Odoo]` · Właściciel biznesowy: `[rola]` · Właściciel techniczny: `[rola]` · Wersja kontraktu: `[v]`

## 1. Po co integracja istnieje?

Proces: `[PROC-...]` · Wyzwalacz: `[np. potwierdzenie płatności]` · Oczekiwany skutek: `[np. jedno zamówienie w Odoo]`

Systemy/środowiska: `[źródło] → [odbiorca]` · Source of truth per pole: `[lista]`

## 2. Kontrakt

| Obiekt / pole | Źródło | Cel | Transformacja | Wymagane? | Przykład (zmyślony) |
|---|---|---|---|---|---|
| Identyfikator | `[pole]` | `[pole]` | `[reguła]` | Tak | `ORDER-001` |
| Status | `[pole]` | `[pole]` | `[mapa statusów]` | Tak | `paid` |

Stabilny klucz/idempotencja: `[source_system + external_id + typ operacji]`

Schemat/versioning: `[format, wersje, kompatybilność]`

Kanał: `JSON-2 / inny API / webhook / polling / EDI / batch`

Wolumen/peak/latencja: `[liczby / jednostka / źródło]`

Odoo wersja, hosting i plan dostępu API: `[potwierdzono]`

## 3. Błędy i odzyskiwanie

| Sytuacja | Oczekiwane zachowanie | Właściciel / alarm |
|---|---|---|
| Timeout po zapisie | `[weryfikacja statusu przed retry]` | `[rola]` |
| Duplikat zdarzenia | `[jeden skutek biznesowy]` | `[rola]` |
| 429 / 5xx | `[backoff, kolejka, próg alertu]` | `[rola]` |
| Błąd walidacji | `[DLQ, poprawa danych, replay]` | `[rola]` |
| Konflikt statusów | `[source of truth / reguła konfliktu]` | `[rola]` |
| Zewnętrzny system offline | `[degradacja/obejście/rekonsyliacja]` | `[rola]` |

Retry / limit / DLQ: `[parametry uzgodnione i sprawdzone]`

Monitoring: `[metryka, próg, właściciel, kanał eskalacji]`

Rekonsyliacja: `[częstotliwość, sumy, statusy, właściciel]`

## 4. Bezpieczeństwo

Konto techniczne i minimalny zakres: `[opis ról]`

Klucze / rotacja / retencja / dostęp: `[procedura bez wartości sekretów]`

Dane osobowe i regulacje: `[wymogi, właściciel]`

## 5. Odbiór

- [ ] Kontrakt i źródło prawdy zatwierdzone.
- [ ] Testy powtórzenia, timeoutu, limitów i konfliktów zakończone.
- [ ] Monitoring / replay / raport uzgodnienia działają.
- [ ] Dostęp botów przetestowany w negatywnych scenariuszach.
- [ ] Właściciel i runbook utrzymania są ustaleni.

Podpisy: `[biznes / integrator / security / data]`

Standard: [integracje API](../docs/09-standard-integracji-api.md)
