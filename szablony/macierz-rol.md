# Szablon: macierz ról i uprawnień Odoo

> Wypełniaj w prywatnej dokumentacji projektu. Kontrola dostępu obejmuje modele, rekordy, pola, załączniki, eksporty i API; nie tylko menu.

## Zakres

Spółki / magazyny: `[zakres]` · Wersja Odoo: `[wersja]` · Właściciel: `[rola security]`

| Rola | Obszar / model | Read | Create | Write | Delete | Ograniczenie rekordów | Zatwierdza |
|---|---|---|---|---|---|---|---|
| Handlowiec | `sale.order` (przykład) | T | T | Zależnie od stanu | N | Własna spółka / zespół | Właściciel sprzedaży |
| Magazynier | Dokumenty magazynowe | T | N/T | Zakres procesu | N | Własny magazyn | Właściciel magazynu |
| Księgowość | Faktury | T | Zależnie od roli | Zależnie od stanu | N | Uprawnione spółki | Właściciel finansów |
| Bot sklepu | Zamówienia / stany | Minimalny | Minimalny | Minimalny | N | Dozwolone kanały | Tech lead + security |

T = Tak; N = Nie. Wiersze są przykładem dyskusji, nie zaleceniem konkretnych ustawień produkcyjnych.

## Testy obowiązkowe

- [ ] Użytkownik z jedną grupą ma wyłącznie wymagany dostęp.
- [ ] Użytkownik z wieloma grupami nie dostaje nieoczekiwanego dostępu.
- [ ] Spółka A nie widzi danych spółki B (lista, eksport, raport, adres, załącznik, API).
- [ ] Public / portal nie może odczytać cudzych rekordów.
- [ ] `sudo()` i akcje automatyczne nie obchodzą kontroli bez uzasadnienia.
- [ ] Konto integracyjne nie ma uprawnień do finansów/HR, jeśli ich nie potrzebuje.
- [ ] Odebranie roli lub usunięcie użytkownika ogranicza dostęp.

Zatwierdzenie: `[security / właściciel biznesowy / data]`

Zobacz: [bezpieczeństwo](../docs/10-bezpieczenstwo.md)
