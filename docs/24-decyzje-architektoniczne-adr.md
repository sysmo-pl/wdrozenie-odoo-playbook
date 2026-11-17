# Decyzje architektoniczne (ADR) we wdrożeniach Odoo

Zapis decyzji architektonicznej (Architecture Decision Record, ADR) dokumentuje, dlaczego zespół wybrał dane rozwiązanie, jakie warianty odrzucił i jakie są konsekwencje dla kosztu, bezpieczeństwa i aktualizacji. Poniżej trzy przykłady: źródło prawdy dla stanów magazynowych, zatwierdzanie rabatu i integracja z systemem, który bywa niedostępny.

ADR najbardziej przydaje się po roku, gdy trzeba aktualizować Odoo lub wyjaśnić klientowi koszt utrzymania niestandardowego modułu.

Przed podobną decyzją sprawdź funkcję w swojej wersji Odoo. Własne wpisy zakładaj na podstawie [szablonu ADR](../szablony/adr-decyzja-architektoniczna.md).

## ADR-001: źródło prawdy dla stanów magazynowych w e-commerce

Status: przykład zaakceptowanej decyzji w fikcyjnym projekcie. Kontekst: sklep internetowy i Odoo pokazują różne dostępne stany, bo każdy stosuje inną definicję rezerwacji. Cel: klient nie widzi ilości, których nie można obiecać do wysyłki.

Rozważone warianty:

1. Sklep jako źródło stanu; Odoo tylko otrzymuje zamówienie. Odrzucone: ryzyko rozjazdu z magazynem.
2. Odoo jako źródło stanu do sprzedaży, sklep jako odbiorca odczytu: rekomendowane do sprawdzenia w procesie.
3. Dwa systemy dwukierunkowo aktualizują stan. Odrzucone: konflikt kolejności i odpowiedzialności.

Decyzja demonstracyjna: Odoo jest źródłem uzgodnionej definicji dostępności. Sklep otrzymuje stan publikowany z uwzględnieniem rezerwacji i ustalonego bufora. Konsekwencje: konieczna obsługa opóźnienia, awarii łącza i reguły cache. Dowód: test sprzedaży w szczycie, częściowych wydań i braku połączenia przez 30 minut. Warunek przeglądu: zmiana magazynu/WMS lub metody rezerwacji.

## ADR-002: zatwierdzanie wyjątkowego rabatu

Status: przykład propozycji oczekującej na decyzję. Kontekst: handlowiec chce zatwierdzić rabat powyżej limitu, a kierownik ma zostawić ślad decyzji. Cel: zapobiec samodzielnemu zatwierdzeniu własnej transakcji.

Warianty: standard i konfiguracja (należy pokazać na docelowej wersji); Studio (jeśli dostępne i spełnia walidację); dedykowany moduł Python (jeśli wymaga serwerowej reguły odpornej na API/importy); proces poza systemem (koszt operacyjny i słabszy audyt).

Rekomendacja warunkowa: najpierw demonstracja standardu, następnie prostszej konfiguracji. Custom Python tylko po dowodzie luki oraz uzgodnieniu testu roli, audytu, dwóch spółek i kosztu aktualizacji. Akceptujący: kierownik sprzedaży + security + tech lead. Status: `OPEN` do czasu demo, a nie „wpisano w backlog developmentu”.

## ADR-003: integracja ERP z systemem zamówień, który bywa niedostępny

Status: przykład zaakceptowanej decyzji w fikcyjnym projekcie. Kontekst: zamówienia docierają czasem po kilku godzinach; po timeoutcie operator uruchamia import ponownie. Cel: brak duplikacji skutku biznesowego i możliwość uzgodnienia każdego dnia.

Warianty: synchronizacja ręczna CSV (niewystarczająca skala); wywołania synchroniczne z bezwarunkowym retry (ryzyko duplikatów); kolejka z trwałym external ID, idempotencją, monitoringiem i raportem uzgodnienia (rekomendowana).

Decyzja demonstracyjna: kolejka i stan zdarzenia są utrwalane; retry nie może powodować powstania drugiego zamówienia. Konsekwencje: dodatkowy koszt utrzymania kolejki, alertowania i ręcznych wyjątków. Dowód: 3 testy awarii, utrata odpowiedzi, restart workera i uzgodnienie dzienne. Warunek przeglądu: zmiana API, wzrost wolumenu, nowe SLA.

## Reguły prowadzenia ADR

- Nadaj ID, datę rzeczywistej decyzji i status `PROPOSED`, `ACCEPTED`, `SUPERSEDED` lub `REJECTED`.
- Opisuj kontekst i alternatywy uczciwie: wariant może być technicznie możliwy, lecz nieopłacalny.
- Odróżniaj decyzję przykładową od faktycznie zatwierdzonej przez klienta.
- Wpisz skutki dla kosztu utrzymania, hostingu, uprawnień i aktualizacji.
- Po zmianie wersji Odoo przeglądaj ADR, których przesłanki mogły się zmienić.

Dalsza lektura: [decyzja standard / Studio / Python](19-drzewo-decyzyjne-customizacja.md), [architektura](05-architektura-srodowiska.md), [integracje](09-standard-integracji-api.md).
