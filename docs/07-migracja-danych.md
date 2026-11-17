# Migracja danych do Odoo: runbook, uzgodnienia i plan cutover

Migracja danych do Odoo to powtarzalny proces: inwentaryzacja, profilowanie, mapowanie, próba na próbce, pełna próba (dry-run), uzgodnienie wyników z właścicielami danych i dopiero potem przełączenie (cutover). Import musi dać się powtórzyć bez tworzenia duplikatów.

Każdy zbiór danych ma właściciela po stronie klienta, który odbiera wynik; za technikę odpowiada migrator lub architekt. Nie przenosimy danych tylko dlatego, że da się je wyeksportować.

## 1. Co zwykle migrować (decyzja, nie automatyzm)

| Obiekt | Pytania kontrolne | Przykład weryfikacji |
|---|---|---|
| Kontrahenci | Duplikaty, kraj, NIP, adresy, kontakt, spółki, zgody | Liczność unikalnych, próbka relacji, uprawnienia |
| Produkty / warianty | SKU, EAN, UoM, kategorie, VAT, aktywne, zamienniki | Unikalność SKU, zgodność ilości i jednostek |
| Cenniki | Data, waluta, grupa klienta, reguły rabatowe | 10 kontrahentów × 10 produktów w porównaniu |
| Zapasy / partie | Magazyny, lokalizacje, rezerwacje, lot/serial, wycena | Suma jednostek i wartości per magazyn |
| BOM / marszruty | Rewizje, jednostki, struktura, komponenty | Zdolność wykonania próbnego zlecenia |
| Otwarte zamówienia | Status, ilość zrealizowana, dostawa, zależności | 100% krytycznych otwartych pozycji |
| Salda, należności, zobowiązania | Data cutover, okres, waluty, faktury otwarte | Uzgodnienie księgowe przez właściciela finansów |
| Historia i załączniki | Wymogi prawne i potrzeba operacyjna, koszt | Przegląd próbek, dostępność archiwum |

## 2. Minimalna specyfikacja mapowania

Każde pole ma: system/obiekt źródłowy → nazwa i typ pola źródłowego → obiekt/pole Odoo → reguła transformacji → obsługa wartości pustych → reguła walidacji → właściciel → przykład testowy. Osobno dokumentuj źródło prawdy i mapę identyfikatorów zewnętrznych (`source_system`, `source_id`, `odoo_id`, wersja/czas synchronizacji).

**Nie polegaj wyłącznie na nazwach kontrahentów jako kluczach.** Ustal identyfikację i sposób obsługi konfliktów oraz zmian nazw. Dla rekordów powiązanych przenoś zależności we właściwej kolejności.

## 3. Kroki migracji

1. Inwentaryzacja: lista zbiorów, ilości, struktury, formaty, źródła, ograniczenia dostępu.
2. Profilowanie: pustki, wartości nietypowe, duplikaty, błędne jednostki, brakujące referencje.
3. Decyzje i czyszczenie: kto rozwiązuje niejednoznaczność? Zasady są zatwierdzone przed importem.
4. Mapa transformacji: pole, reguła, skutek dla raportów i testów.
5. Próba na próbce: celowo uwzględnij złe dane, wyjątki, numery seryjne, różne waluty/spółki.
6. Pełny dry-run: czas importu, liczba błędów, stopień kompletności, wydajność.
7. Uzgodnienia: liczność, suma ilości, wartość, salda, dokumenty bez relacji, właściciele podpisują wynik.
8. Korekta i retest: przyczyna błędu, zmiana reguły, ponowienie bez tworzenia dubletów.
9. Plan finalnej migracji: cutover, freeze, delta, wyłączenie integracji, komunikaty i okno awaryjne.
10. Po starcie: smoke test procesów, raport rozbieżności, ograniczenie możliwości zmiany starego systemu.

## 4. Przykładowy raport z dry-run

| Zbiór | Źródło | Odoo | Odchylenie | Przyczyna / decyzja |
|---|---:|---:|---:|---|
| Produkty aktywne | 8 000 | 7 995 | 5 | 5 zduplikowanych SKU: naprawa przed GO |
| Kontrahenci aktywni | 3 100 | 3 100 | 0 | Próba ręczna 30 rekordów zaliczona |
| Otwarte zamówienia | 380 | 379 | 1 | Brakujący produkt: blokada GO do wyjaśnienia |
| Zapasy [szt.] | 52 500 | 52 500 | 0 | Kontrola dodatkowa wartości per magazyn |

Ważne: zgodność liczności nie dowodzi zgodności wartości, relacji, statusów ani uprawnień. Uzgodnij sumy per spółka/waluta/lokalizacja, próbki złożone i pełny proces użytkownika na danych po imporcie.

## 5. Reguły przetwarzania błędów

- Rekord z nieznaną jednostką lub podatkiem nie może być po cichu „domyślnie” naprawiony.
- Nieznane relacje oznacz jako błędy do decyzji, nie kasuj powiązań.
- Prowadź rejestr odrzuconych rekordów z przyczyną i właścicielem, w systemie prywatnym.
- Migrator musi umieć uruchomić proces ponownie bez dublowania danych (idempotencja importu).
- Każda próba ma numer wersji mapowania, źródło danych, datę oraz raport odchyleń.
- Kluczowe dane finansowe i podatkowe zatwierdza uprawniony specjalista klienta.

## 6. Plan finalnego cutover

- T-14: pełny dry-run i wstępne uzgodnienia.
- T-7: finalne mapy, próba generalna, okno zamrożenia, odpowiedzialności.
- T-1: kopia i procedura odtworzenia, kontrola dostępu, zamknięcie krytycznych zmian.
- T0: zamrożenie systemu źródłowego → ekstrakcja delty → import → uzgodnienie → testy → decyzja GO/NO-GO.
- T+1: kontrola zamówień, faktur, zapasów, kolejek i uprawnień.
- T+7: przegląd rozbieżności i formalny odbiór migracji.

Czasy są przykładową sekwencją, a nie obietnicą terminu. Gdy w trakcie startu wykonano nieodwracalne operacje w nowych systemach, powrót do starej aplikacji może wymagać rekonsyliacji danych i planu kompensacji, a nie prostego restore. Zdefiniuj punkt bezpiecznego odwrotu przed startem.

## Bramka przed Go-Live

- [ ] Każdy zbiór ma właściciela i zaakceptowane mapowanie.
- [ ] Uzgodniono kompletność, sumy, wartości i relacje; odchylenia mają decyzje.
- [ ] Migracja jest powtarzalna i przetestowano ponowienie bez duplikacji.
- [ ] Najważniejsze procesy działają na danych po migracji.
- [ ] Backup bazy i filestore oraz test odtworzenia są udokumentowane.
- [ ] Istnieją freeze window, plan delty, procedura awaryjna i komunikacja.

Przykłady i kontrola: [pułapki migracji](22-migracja-problemy-kontrola.md) · [przykład otwartych zamówień](../przyklady/migracja-otwarte-zamowienia.md).
