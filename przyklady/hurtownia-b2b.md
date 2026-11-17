# Przykład wdrożenia Odoo ERP: hurtownia B2B z dwoma magazynami

Firma i wszystkie liczby są zmyślone. Przykład pokazuje cały projekt od kick-offu do odbioru.

## 1. Kontekst i cel

Fikcyjna firma Hurtownia Delta sprzedaje towary firmom przez handlowców i sklep B2B. Ma dwa magazyny, 24 użytkowników Odoo, 8 000 aktywnych SKU, około 300 zamówień dziennie i dwa razy tyle podczas sezonowego szczytu. Wcześniej zespół używał osobnego programu magazynowego, fakturowania i arkuszy do limitów kupieckich.

Wynik biznesowy fazy 1: pojedynczy proces od zamówienia do faktury, uzgadnialne stany i brak duplikatów po błędach integracji.

KPI startowe (hipotetyczne): 25 ręcznych korekt zamówień na 1 000; 10–20 minut przetwarzania zamówienia; kilka rozbieżności stanów tygodniowo. KPI docelowe: wartości do zatwierdzenia na discovery, nie marketingowa obietnica.

## 2. Kick-off: fakty, które trzeba ustalić

Sponsor: dyrektor operacyjny. Właściciel procesów sprzedażowych: kierownik sprzedaży. Magazyn: kierownik logistyki. Dane: szef master data. Finanse: główna księgowa. Decyzje IT: architekt klienta. 

Ustalenie: firma nie zaczyna od wyboru przycisków, lecz od przepływu „sklep → Odoo → magazyn → faktura → klient”. Każdy krok ma jednego właściciela, kryterium odbioru i wyjątki.

## 3. Fragment warsztatu: przykładowe pytania

| Pytanie konsultanta | Przykładowa odpowiedź | Co dalej zapisujemy |
|---|---|---|
| Co się dzieje, gdy sklep wyśle zamówienie dwukrotnie? | „Czasem magazyn dostaje dwa zamówienia”. | `REQ-INT-01`: jedno zamówienie dla jednego ID sklepu, test retry |
| Kiedy rezerwujecie towar? | „Po potwierdzeniu płatności, ale dla klientów kredytowych od razu”. | `PROC-SAL-01`: dwa warianty rezerwacji, reguły płatności |
| Kto może zatwierdzić zamówienie przekraczające limit? | „Kierownik, zwykle e-mailem”. | `REQ-SAL-02`: rolowa akceptacja i audyt decyzji |
| Czy dostawa częściowa jest dopuszczalna? | „Tak, ale klient musi zobaczyć brak”. | `REQ-WH-03`: status pozostałej ilości i powiadomienie |
| Jak mierzycie stan magazynu? | „W obu systemach jest inna definicja dostępności”. | `DEC-01`: Odoo source of truth dla stanu do wysyłki |
| Czy sprzedajecie w dwóch spółkach? | „Obecnie w jednej, druga planowana za pół roku”. | `RISK-04`: przyszłe multi-company uwzględnić w architekturze |

Pytanie kontrolne: „Pokażcie ostatnie zamówienie z brakującym towarem i wyjaśnijcie, kto je poprawiał”. Jeżeli brak przykładu, scenariusz pozostaje hipotezą do weryfikacji.

## 4. FIT/GAP: nie od razu Python

| Wymaganie | Decyzja demonstracyjna | Powód |
|---|---|---|
| Cennik per grupa klienta | Najpierw sprawdzić konfigurację standardową | Odoo ma mechanizmy cenników; szczegóły zależą od wersji |
| Dwa magazyny i rezerwacje | Najpierw standard i konfiguracja | Nie powielać własnej logiki stanów |
| Limit kredytowy z rozbudowanymi wyjątkami | FIT/GAP i demonstracja, ewentualnie Python | Potrzebna ścisła reguła oraz log decyzji |
| Zamówienia ze sklepu | Integracja API z idempotencją | Sklep pozostaje właścicielem ID zamówienia |
| Śledzenie historii korekt | Najpierw weryfikacja dostępnych funkcji i audytu | Należy chronić integralność dokumentów |
| Raport dziennych rozbieżności | Konfiguracja/raport/uzgodnienie | Dowód zgodności integracji jest ważniejszy niż ekran |

Decyzji o custom Python nie podejmujemy bez demonstracji i oceny wpływu na aktualizacje, uprawnienia oraz całkowity koszt utrzymania.

## 5. Kontrakt przykładowej integracji sklep → Odoo

Źródło prawdy: sklep odpowiada za ID zamówienia i płatność, Odoo za stan magazynu, realizację i fakturę. Obie strony muszą potwierdzić mapę statusów. Przykładowy unikalny identyfikator zdarzenia: `shop_b2b:ORDER-00017:created`.

Najtrudniejszy przypadek: sklep wysyła zamówienie, Odoo zapisuje rekord, ale odpowiedź ginie. Sklep ponawia wysyłkę. Projekt musi zapewnić jedno zamówienie w Odoo i jeden łańcuch realizacji, a operator powinien zobaczyć historię prób.

Inne testy: zamówienie z nieznanym SKU, 429 po stronie API, awaria przez 2 godziny, zmiana statusu podczas ponowienia, korekta klienta po częściowym wydaniu.

Uzgodnienie nocne (przykład): ilość nowych zamówień per kanał i dzień, unikalne external IDs, zamówienia bez odpowiednika, rozbieżność statusów. Monitoring musi zgłosić każdą „osieroconą” transakcję.

## 6. Migracja: co robimy przed dniem przełączenia

1. Z 8 000 produktów wyznacz stabilny SKU oraz mapę wariantów, UoM i identyfikatorów.
2. Zweryfikuj, czy kontrahent posiada unikalny identyfikator; duplikaty rozstrzyga właściciel master data.
3. Stany per magazyn i partie uzgadnia kierownik magazynu, nie programista.
4. Otwarte zamówienia przenieś z informacją o już wydanej ilości i statusie, jeśli architektura tego wymaga.
5. Salda i dokumenty finansowe wymagają odrębnego odbioru przez księgowość.
6. Wykonaj dry-run pełnej próby i porównaj liczbę rekordów, ilości, wartości i relacje, nie same liczniki.

Przykład defektu blokującego: w dry-run jeden produkt był mapowany do dwóch SKU, przez co zsumowany stan wyglądał poprawnie, lecz partie przypisano błędnie. Po wykryciu wstrzymano migrację tej grupy towarów i doprecyzowano mapę.

## 7. UAT: testy podpisywane przez biznes

| UAT | Wykonuje | Warunek zaliczenia |
|---|---|---|
| `UAT-01` Standardowe zamówienie B2B | Handlowiec + magazynier | Jeden dokument na etap, poprawne statusy i kwoty |
| `UAT-02` Brak towaru / wydanie częściowe | Magazynier | Nie wydano niedostępnej ilości; pozostałość jawna |
| `UAT-03` Timeout i retry | Integrator + key user | Jedno zamówienie; brak dubletów |
| `UAT-04` Przekroczenie limitu | Handlowiec + kierownik | Handlowiec nie zatwierdza własnego wyjątku |
| `UAT-05` Uzgodnienie faktury | Księgowość | Kwoty i statusy zgodne z regułami klienta |
| `UAT-06` Brak dostępu | Użytkownik testowy | Magazynier nie uzyskuje uprawnień księgowych |

Wszystkie testy mają ID wymagań, stan początkowy, kroki, dowód, wynik oraz status defektów.

## 8. T0 i wczesne utrzymanie

Przed uruchomieniem: zatwierdzone UAT, sprawdzony backup bazy i filestore, dyżury, dostęp do API, ustawiona kolejka, raport uzgodnienia. Podczas cutover zamrażamy źródło, importujemy deltę, uzgadniamy liczby, włączamy przepływy, testujemy i dopiero wtedy otwieramy użytkowników.

Twarde NO-GO: brak możliwości odtworzenia, brak zgodności stanów, duplikaty zamówień po retry, nieskuteczna separacja ról.

Po starcie: zespół monitoruje najstarsze zaległe zdarzenia, różnice stanów, zamówienia bez faktury i problemy key users. Po spełnieniu uzgodnionych metryk przekazuje obsługę zespołowi utrzymania.

## 9. Jakie artefakty naprawdę powstałyby w projekcie?

- [Karta projektu](../szablony/karta-projektu.md) z zakresem i KPI.
- [Notatki discovery](../szablony/notatka-discovery.md) z właścicielami pytań.
- [Proces i FIT/GAP](../szablony/karta-procesu.md), [rejestr wymagań](../szablony/rejestr-wymagan.md).
- [Karta integracji](../szablony/karta-integracji.md) z idempotencją, retry i uzgadnianiem.
- [Plan migracji](../szablony/plan-migracji.md) z odchyleniami po dry-run.
- Scenariusze UAT *(wkrótce)* podpisane przez key users.
- Checklista startu *(wkrótce)* i formalna decyzja sponsora.
