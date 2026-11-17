# Mapowanie procesów AS-IS / TO-BE oraz FIT/GAP w Odoo

Analiza FIT/GAP porównuje potwierdzone wymagania biznesowe ze standardem Odoo w docelowej wersji i edycji i przypisuje każde wymaganie do jednej z siedmiu kategorii: standard, konfiguracja, Studio, moduł Python, integracja, zmiana procesu albo odrzucenie/odłożenie. Modyfikację wybiera się dopiero po pokazaniu standardu i ocenie całkowitego kosztu utrzymania.

## Używaj języka procesu, nie przycisków

Wymaganie „potrzebujemy przycisku Zatwierdź” jest rozwiązaniem proponowanym przez klienta. Prawdziwym wymaganiem może być „zamówienie na kwotę >20 000 zł musi zatwierdzić kierownik sprzedaży, a osoba wprowadzająca nie może zatwierdzić własnego zamówienia”. Tylko drugie zdanie da się bezpiecznie przetestować i porównać z Odoo.

## Minimum karty procesu

| Pole | Przykład (fikcyjny) |
|---|---|
| ID i nazwa | `PROC-SAL-01`: Zamówienie B2B |
| Cel | Od zapytania do faktury bez podwójnego przepisywania danych |
| Wyzwalacz | Potwierdzenie zamówienia przez klienta |
| Koniec | Faktura i zamówienie w spójnym stanie |
| Role | Handlowiec, kierownik, magazynier, księgowość |
| Systemy | Odoo, sklep B2B, dostawca płatności |
| AS-IS | Zatwierdzenie e-mailem, zamówienie w Excelu, ręczne WZ |
| TO-BE | Odoo zatwierdza limity, rezerwuje, wydaje, przekazuje fakturę |
| Wyjątki | Częściowa dostawa, anulowanie, brak limitu kredytowego |
| Miary | Odsetek błędów, czas obsługi, liczba ręcznych korekt |

### Jak rysować proces

Zapisz kolejność: zdarzenie → czynność → decyzja → dane → status → następna rola. Każde rozwidlenie musi pokazywać także ścieżkę „NIE” i zachowanie w przypadku błędu technicznego. W diagramie wyróżnij wymianę z innym systemem, operację nieodwracalną oraz moment księgowania.

## Siedem kategorii FIT/GAP

| Kategoria | Kiedy wybrać | Co sprawdzić |
|---|---|---|
| FIT: standard | Funkcja działa w Odoo bez modyfikacji | Edycja, wersja, poprawność procesu i ról |
| FIT: konfiguracja | Wystarczy ustawienie, reguła, grupa lub istniejący workflow | Ryzyko skutków ubocznych, przenoszalność |
| FIT: Studio | Zmiana UI / pól / automatyzacji w zakresie wspieranym przez daną edycję | Hosting, licencja, testy zmian, złożoność |
| GAP: dedykowany Python | Wymagana nowa spójna reguła biznesowa lub model | Testy, ACL, multi-company, upgrade, utrzymanie |
| GAP: integracja | Funkcja należy do innego systemu | Właściciel danych, błędy, idempotencja, API |
| GAP: zmiana procesu | Modyfikacja organizacyjna daje równoważny wynik | Szkolenie, kontrola, raportowanie |
| Odrzucone / później | Korzyść nie uzasadnia kosztu/ryzyka w MVP | Właściciel decyzji, ryzyko rezygnacji |

## Algorytm decyzji o customizacji

1. Czy wymaganie opisuje wynik biznesowy, a nie interfejs?
2. Czy aktualna wersja i edycja Odoo realizuje go standardowo? Pokaż dowód na stagingu.
3. Czy proces biznesowy można uprościć bez straty kontroli i zgodności?
4. Czy wystarczy konfiguracja, dopiero potem Studio? Oceń ograniczenia hostingu.
5. Czy custom Python jest naprawdę potrzebny? Jeżeli tak, zdefiniuj kontrakt, role, testy, migracje danych i koszt kolejnego upgrade'u.
6. Jeśli dotyczy systemu zewnętrznego: czy integracja jest lepsza od dublowania danych w Odoo?
7. Czy akceptujący zna całkowity koszt utrzymania, a nie tylko koszt wytworzenia?

## Reguła „pięciu dowodów” dla REQ

Nie oznaczaj wymagania jako `READY`, dopóki nie ma: (1) właściciela biznesowego, (2) przykładowych danych/test case, (3) kryterium akceptacji, (4) planu rozwiązania i (5) informacji o uprawnieniach i skutkach dla danych. Dla REQ o wysokim ryzyku wymagaj także decyzji o wariancie awaryjnym.

## Analiza wpływu zmiany (CR)

Zanim przyjmiesz „małą zmianę”, sprawdź: które procesy dotknie; czy trzeba zmienić architekturę / model danych / integracje; czy wpłynie na księgowanie, podatki lub stany; czy wymaga migracji; jak zmieni scenariusze UAT; ile kosztuje utrzymanie po aktualizacji Odoo; kto finansuje i akceptuje.

## Przykład dobrego wymagania

REQ-042: blokada wydania ponad limit kredytowy (fikcyjny)

- Cel: ograniczyć niekontrolowane należności.
- Reguła: gdy suma należności przeterminowanych kontrahenta przekracza ustalony próg, potwierdzenie wydania wymaga zgody roli „Kierownik kredytu”.
- Pytania otwarte: czy limit liczymy per spółka, waluta, grupa kontrahentów; co z opóźnionymi importami płatności; czy można wydać częściowo?
- Kryterium akceptacji: handlowiec nie obchodzi blokady; kierownik może zatwierdzić i widoczny jest ślad audytowy; scenariusz testowy obejmuje zmianę kursu i opóźnioną płatność.
- Warianty rozwiązania: reguła standardowa, konfiguracja, dedykowany moduł, decyzja po demonstracji i konsultacji biznesowej.

## Czego nie robić

- Nie odtwarzaj bez analizy wszystkich wyjątków z legacy: część może być obejściem starego błędu.
- Nie nazywaj funkcji „standardową” bez sprawdzenia w docelowej edycji i wersji.
- Nie używaj uprawnień administratora jako rozwiązania brakującej roli biznesowej.
- Nie wrzucaj do backlogu „integracja z ERP” bez danych, mapowania, awarii i właściciela.
- Nie akceptuj „działa na moim koncie” jako dowodu testu uprawnień.

Dalsza decyzja: [drzewo standard / Studio / Python / API](19-drzewo-decyzyjne-customizacja.md) · [trzy przykładowe ADR](24-decyzje-architektoniczne-adr.md).
