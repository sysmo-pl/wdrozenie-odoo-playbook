# Scenariusz projektowy: migracja częściowo zrealizowanych zamówień do Odoo

## Kontekst

Firma przechodzi z poprzedniego ERP do Odoo. Zamówienie `LEG-231` ma 10 sztuk, z czego 6 wysłano i 4 oczekują. Cel: odtworzyć tylko właściwy otwarty proces, bez ponownego wydania już dostarczonych pozycji ani ponownego fakturowania.

## Ustalenia konieczne przed decyzją

- Czy dokumenty historyczne pozostają w starym ERP lub archiwum?
- Czy Odoo przejmuje realizację wyłącznie otwartych ilości, czy także historię?
- Czy część wysłana została już zafakturowana i czy istnieje korekta?
- Jak zachować identyfikator źródłowy i unikać podwójnego importu?
- Kto potwierdza salda i podatki w otwartych dokumentach?

## Test i kryterium odbioru

| Działanie | Oczekiwany rezultat |
|---|---|
| Import `LEG-231` z atrybutami statusu | Rozpoznana sytuacja częściowej realizacji zgodnie z uzgodnioną metodą migracji |
| Próba realizacji reszty | Nie można ponownie wydać już dostarczonych 6 sztuk |
| Ponowne uruchomienie importu | Brak duplikatu dokumentu/skutku |
| Uzgodnienie rozrachunków | Finanse potwierdzają poprawność stanów i wartości |
| Kontrola po cutover | Źródłowy i docelowy system mają jawnie rozdzieloną odpowiedzialność |

Ważne: sposób technicznego odtworzenia dokumentu zależy od modułów i wersji oraz nie zawsze polega na prostym imporcie historycznego statusu. Przed startem wykonaj próbę na kopii danych zanonimizowanych. **NO-GO:** nieuzgodniona możliwość ponownego wydania lub fakturowania.
