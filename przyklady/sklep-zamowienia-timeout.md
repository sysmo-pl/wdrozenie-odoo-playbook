# Scenariusz projektowy: sklep internetowy → Odoo, timeout i ponowne zamówienie

## Kontekst i cel

Fikcyjny sklep A ma 2 500 zamówień dziennie, szczyt 8 000. Sklep nadaje `external_order_id`, Odoo prowadzi realizację i dokumenty sprzedaży. Wynik biznesowy: jeden identyfikator zamówienia sklepu skutkuje dokładnie jednym zamówieniem Odoo, niezależnie od liczby ponowień.

## Discovery: pytania

1. Kiedy sklep uznaje zamówienie za złożone: przed płatnością czy po?
2. Który system jest właścicielem danych nabywcy, płatności, podatku i dostawy?
3. Czy zamówienie ma jeden identyfikator w całym sklepie, czy tylko w kanale/oddziale?
4. Jakie statusy mogą się cofnąć i kto je zatwierdza?
5. Czy mamy możliwość pobrania pojedynczego zamówienia z API sklepu w celu uzgodnienia?

## Decyzja FIT/GAP

Nie pisać osobnego mechanizmu „podwójnego sprawdzenia w Odoo UI”. W kontrakcie integracji zdefiniować trwały klucz `(sklep, external_order_id)` i ochronę atomową przed wyścigiem. Jeśli Odoo nie oferuje wymaganej atomowej operacji przez dostępne API, rozważyć warstwę integracyjną lub odpowiednią własną metodę serwerową na hostingu, który na to pozwala.

## Scenariusz akceptacyjny

| Krok | Działanie testowe | Oczekiwany wynik |
|---|---|---|
| 1 | Przyjęcie zamówienia `A-1001` | Powstaje jedno zamówienie w Odoo |
| 2 | Symulowane zagubienie odpowiedzi po zapisie | Integrator nie zna wyniku, stan `UNKNOWN`/do uzgodnienia |
| 3 | Trzy ponowienia `A-1001` | Brak nowego zamówienia i brak drugiej realizacji |
| 4 | Jednoczesne przyjęcie dwóch wiadomości z tym samym ID | Jeden skutek, druga wiadomość bezpiecznie rozpoznana |
| 5 | Błędna pozycja SKU | Walidacja błędu i obsługa ręczna, bez częściowego potwierdzenia niedozwolonego procesu |
| 6 | Raport dzienny | Zlicza wszystkie `external_order_id` bez nieuzasadnionych rozbieżności |

Dowody: stan kolejki, mapa identyfikatorów, historia prób (bez danych osobowych), zrzut danych testowych. **NO-GO:** choćby jeden niewyjaśniony duplikat zamówienia w scenariuszu testowym.

Powiązane: [scenariusz AWR-01](../docs/21-incydenty-integracji-api.md), [karta integracji](../szablony/karta-integracji.md).
