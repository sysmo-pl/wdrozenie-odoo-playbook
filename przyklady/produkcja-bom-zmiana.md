# Scenariusz projektowy: zmiana BOM w trakcie realizacji produkcji Odoo

## Kontekst

Fikcyjny producent montuje urządzenia z komponentów. W ciągu dnia zmienia się wersja listy materiałowej (BOM), a część zleceń ma już rezerwacje lub pobrane komponenty. Cel: nowa definicja produktu nie może niejawnie zmieniać historii ani zużycia rozpoczętych zleceń.

## Pytania konsultanta

- Kto zatwierdza wersję BOM i datę wejścia w życie?
- Czy zmiana dotyczy nowych zamówień, już zaplanowanych czy również rozpoczętych?
- Czy materiały są rozliczane ilościowo, kosztowo, po partiach lub numerach seryjnych?
- Jak rozróżniacie zamienniki materiałowe od zmiany technologicznej?
- Czy wymaga to kontroli jakości, zgodności i audytowalności?

## FIT/GAP

Najpierw zweryfikować standardowe mechanizmy BOM, MRP i zmian technologicznych w docelowej wersji i edycji. Ewentualne rozszerzenie Python musi mieć ograniczony zakres: np. dodatkowa blokada uruchomienia produkcji bez zatwierdzonej wersji, a nie zastępowanie silnika zapotrzebowania materiałowego.

## UAT

| ID | Test | Wynik wymagany |
|---|---|---|
| `UAT-MRP-01` | Uruchom zlecenie na wersji BOM A | Zużycia i rezerwacje zgodne z wersją przyjętą dla procesu |
| `UAT-MRP-02` | Zatwierdź wersję BOM B | Nowe zlecenia stosują uzgodnioną regułę wyboru wersji |
| `UAT-MRP-03` | Spróbuj zmienić rozpoczęte zlecenie bez zgody | Brak cichej zmiany historycznego zapotrzebowania |
| `UAT-MRP-04` | Zwróć komponent i użyj zamiennika | Uzgodniony ślad partii, kosztów i wyjątku |
| `UAT-MRP-05` | Użytkownik bez uprawnień edytuje BOM | Odmowa dostępu, również poza widokiem UI |

Warunek akceptacji: kierownik produkcji i kontrola jakości zatwierdzają przykłady przed wdrożeniem. Ryzyko: modyfikacja podstawowych rozliczeń bez testu kosztów i traceability.
