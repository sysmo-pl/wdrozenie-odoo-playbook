# Odoo: standard, konfiguracja, Studio, moduł Python czy integracja API?

Dedykowany moduł Odoo w Pythonie ma sens wtedy, gdy wymaganie biznesowe jest potwierdzone, standard i konfiguracja nie wystarczają, a potrzebna jest spójna logika serwerowa lub model danych, którego nie można rozsądnie zrealizować prostszym sposobem. Wybór musi uwzględniać hosting, edycję, bezpieczeństwo i koszt aktualizacji.

Uzupełnienie do rozdziałów [FIT/GAP](04-mapa-procesow-fit-gap.md) i [standard modułów Python](08-standard-modulow-python.md). Przykłady są fikcyjne i nie są obietnicą dostępności funkcji w każdej wersji Odoo.

## Drzewo decyzyjne: osiem pytań w dobrej kolejności

1. Czy potrafimy opisać wynik biznesowy i test akceptacyjny? Jeśli nie, wracamy do discovery; nie wybieramy technologii.
2. Czy obecne Odoo rozwiązuje to w standardzie? Testujemy demonstracyjnie na docelowej wersji, edycji i konfiguracji spółek.
3. Czy bezpieczna zmiana procesu daje równoważny wynik? Jeśli tak, nie utrzymujemy własnego kodu bez potrzeby.
4. Czy wystarczy konfiguracja? Sprawdzamy ustawienia, role, cenniki, reguły, istniejące automatyzacje i raporty.
5. Czy Studio jest dostępne i wystarczające? Weryfikujemy hosting, plan, ograniczenia automatyzacji, walidacje serwerowe, testy i przenośność zmian.
6. Czy problem leży w innym systemie? Jeśli jego dane są źródłem prawdy, rozważamy kontrakt integracyjny zamiast duplikacji logiki w Odoo.
7. Czy potrzebny jest dedykowany moduł? Opisujemy granice odpowiedzialności, model bezpieczeństwa, testy, wydajność i strategię aktualizacji.
8. Czy rozwiązanie zostaje zaakceptowane po uwzględnieniu TCO? Koszt wykonania to nie wszystko; dołączamy wsparcie, zmiany w prawie, utrzymanie i upgrade.

Jeżeli nie ma dowodu dla pytania 2 lub 5, decyzja pozostaje OPEN, nie „wybrano Python”.

## Macierz wariantów

| Kryterium | Standard Odoo | Konfiguracja | Studio | Moduł Python | Integracja API |
|---|---|---|---|---|---|
| Typowy cel | Odtworzenie wspieranego procesu | Dopasowanie ustawień i reguł | Pola, widoki, proste zmiany w granicach możliwości | Nowa logika/model lub kontrola transakcyjna | Wymiana danych z inną usługą |
| Kto odpowiada | Konsultant + biznes | Konsultant + biznes | Konsultant techniczny | Developer/tech lead | Integrator + właściciele danych |
| Zależność od hostingu | Sprawdź dostępność aplikacji | Zwykle mniejsza | Tylko Enterprise; w Odoo Online plan Custom | Odoo.sh lub on-premise (nie Odoo Online) | JSON-2 od 19; plan Custom w subskrypcji |
| Ryzyko upgrade'u | Zwykle niższe | Zależne od konfiguracji | Zależne od mechanizmu i zakresu | Wymaga testów kompatybilności | Wymaga testów kontraktowych |
| Ryzyko bezpieczeństwa | Uprawnienia i konfiguracja | Reguły | Automatyzacje, widoki i dostęp | ACL, record rules, `sudo()`, metody publiczne | Klucze, role, dane, autoryzacja |
| Wymagany dowód | Demo procesu | Demo roli i wyjątku | Demo + test regresyjny | Testy jednostkowe/integracyjne i review | Kontrakt, retry, uzgodnienie |

Ważne: ograniczenia planu, hostingu i API są zależne od wersji. Od Odoo 19 nowe integracje zewnętrzne powinny korzystać z JSON-2 API; w subskrypcjach Odoo dostęp do zewnętrznego API zapewnia plan Custom. Studio wymaga edycji Enterprise; w cenniku Odoo jest w planie Custom albo w One App Free jako jedyna aplikacja, a plan Standard go nie obejmuje. Zweryfikuj [oficjalną dokumentację](https://www.odoo.com/documentation/20.0/developer/reference/external_api.html) i ofertę przed wyceną.

## Przykłady decyzji FIT/GAP

| Potrzeba | Pierwszy wariant do sprawdzenia | Co zmieni decyzję |
|---|---|---|
| Cennik zależny od grupy klientów | Standardowe cenniki | Wielowarstwowy algorytm uwzględniający dane spoza Odoo |
| Dodatkowe pole na zamówieniu | Konfiguracja / Studio | Pole musi uczestniczyć w restrykcyjnej regule serwerowej |
| Automatyczny raport o brakach | Standardowy raport / konfiguracja | Specjalna agregacja wielu systemów lub wydajność |
| Niestandardowe zatwierdzenie limitu kredytowego | Sprawdzenie standardu | Reguła musi być atomowa, audytowalna i odporna na równoległe operacje |
| Aktualizacja statusu przesyłki od kuriera | Integracja | Kurier nie udostępnia danych lub mają inne znaczenie procesowe |
| Skomplikowany proces „jak w starym ERP” | Zmiana procesu + standard | Obowiązek prawny lub realna przewaga biznesowa uzasadnia odstępstwo |
| Faktura zależna od potwierdzenia z zewnętrznego systemu | Projekt procesu i integracji | Potrzebna jest dodatkowa lokalna reguła utrzymująca stan |
| Przekazanie płatności i rezerwacja towaru | Analiza transakcyjności | Dwa wywołania API nie dają wspólnej transakcji; trzeba zaprojektować skutki i kompensację |

## Minimalna karta decyzji

W każdej decyzji `DEC-XXX` zapisz: problem i kryterium akceptacji, wersję/edycję/hosting, warianty, dowód z demo, powód odrzucenia prostszych metod, ryzyko uprawnień i danych, koszt wytworzenia i utrzymania, osobę akceptującą, datę weryfikacji.

Przykład: `DEC-014`, blokada wydania dla należności przeterminowanych; standard jest niewystarczający dla wielospółkowych wyjątków; proponowany moduł ma kontrolować uprawnienia i ślad zatwierdzenia, nie zastępować mechaniki księgowej. Status: do weryfikacji z księgowością, security i tech leadem. Patrz: [przykłady ADR](24-decyzje-architektoniczne-adr.md) oraz [szablon ADR](../szablony/adr-decyzja-architektoniczna.md).

## Kiedy odmówić customizacji

- Brak właściciela biznesowego, danych testowych lub testu akceptacji.
- Uzasadnienie brzmi tylko „bo tak działał stary system”, bez analizy kosztu procesu.
- Hostowanie nie dopuszcza planowanego typu rozszerzenia.
- Rozszerzenie wymaga trwałego omijania praw dostępu albo niekontrolowanej manipulacji zapisami księgowymi.
- Klient nie planuje utrzymania, a wymóg będzie wymagać aktualizacji po major upgrade.

Wynik warsztatu: nie lista życzeń, lecz decyzje `FIT`, `CONFIG`, `STUDIO`, `CUSTOM`, `INTEGRATION`, `PROCESS`, `DEFER` z kryteriami odbioru. Przy istotnych ryzykach dołącz [kartę zmiany modułu](../szablony/karta-zmiany-modulu.md).
