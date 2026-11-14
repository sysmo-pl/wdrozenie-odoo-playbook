# Konfiguracja obszarów Odoo: co sprawdzić przed customizacją

Konfiguracja Odoo to wdrożenie reguł biznesowych, a nie samo włączenie aplikacji: dla każdego obszaru zapisuje się decyzje, demonstruje proces od początku do końca z wyjątkami i testuje role. KSeF jest w Odoo 18 i nowszych obsługiwany przez standardowy moduł lokalizacji `l10n_pl_edi` (Community i Enterprise).

Przy każdej zmianie zapisz stan początkowy, decyzję, wynik testu i właściciela. Funkcje oraz nazwy menu zależą od wersji, edycji i zainstalowanych aplikacji Odoo.

## 1. CRM i sprzedaż

Decyzje: etapy lejka, źródła leadów, odpowiedzialność handlowców, cenniki, progi rabatowe, waluty, akceptacje ofert, adresy, warunki płatności, fakturowanie przy dostawie lub zamówieniu.

Zademonstruj: lead → szansa → oferta → zamówienie → dostawa/usługa → faktura → płatność. Następnie: zmiana ceny po zatwierdzeniu, anulowanie, rabat ponad limit i role „handlowiec / kierownik”.

Pułapka: proces ofertowy „działa”, ale nie uzgodniono kontroli limitu kredytowego ani źródła statusu płatności.

## 2. Zakupy

Decyzje: reguły zaopatrzenia, progi akceptacji, ceny dostawcy, minimalne ilości, terminy, jednostki miar, przyjęcia częściowe i 3-way matching (jeśli wymagane).

Zademonstruj: zapotrzebowanie → RFQ / PO → przyjęcie → faktura dostawcy → różnica ceny i ilości → zatwierdzenie.

Pułapka: zamówienie zakupu jest gotowe, ale nie ma reguł dla dostaw częściowych i różnic między zamówieniem a fakturą.

## 3. Magazyn

Decyzje: magazyny i lokalizacje, śledzenie partii/serii, jednostki, trasy, zasady rezerwacji, kompletacja, kontrola jakości, inwentaryzacje, zwroty.

Zademonstruj: przyjęcie → odłożenie → rezerwacja → wydanie częściowe → zwrot → korekta stanów i uzgodnienie wartości.

Pułapka: test przechodzi na pojedynczej sztuce, ale nie na produkcie z wariantami, seriami i dwiema lokalizacjami.

## 4. MRP / produkcja

Decyzje: BOM i rewizje, marszruty, gniazda, zużycie materiałów, zamienniki, braki, identyfikowalność, podwykonawstwo.

Zademonstruj: zamówienie → zapotrzebowanie → zlecenie produkcyjne → zużycie → przyjęcie wyrobu → raport różnicy materiałowej.

Pułapka: model BOM nie uwzględnia różnych jednostek zakupowych lub dat wejścia zmiany technologicznej.

## 5. Finanse i fakturowanie

Decyzje: plan kont, podatki, analityka, numery dokumentów, waluty, płatności, korekty, powiązania z magazynem i księgowością zewnętrzną.

Zademonstruj z właścicielem finansowym: sprzedaż z korektą, zakup z różnicą cenową, import wyciągu, częściową płatność, koniec miesiąca i uzgodnienie salda. Nie konfiguruj reguł podatkowych „na intuicję”.

Pułapka: poprawna faktura w PDF nie oznacza prawidłowego dekretu, podatku, statusu e-faktury ani poprawnego zamknięcia okresu.

## 6. KSeF w Odoo

W Odoo 18 i nowszych obsługę Krajowego Systemu e-Faktur (KSeF) zapewnia standardowy moduł lokalizacji polskiej `l10n_pl_edi`, w edycji Community i Enterprise. W Odoo 16–17 standardowej obsługi nie ma: potrzebny jest moduł dodatkowy (zewnętrzny lub dedykowany) albo aktualizacja do wersji 18 lub nowszej. Moduł `l10n_pl_edi` (FA(3)) jest w aktualnym kodzie serii 18.0: starsze instalacje Odoo 18 trzeba zaktualizować do bieżącego kodu tej serii. Sama konfiguracja techniczna nie wystarcza: reguły wystawiania, korekt i trybów awaryjnych musi zatwierdzić księgowość klienta.

Co robi moduł standardowy (wg dokumentacji Odoo 20.0): generuje fakturę w strukturze FA(3), wysyła ją do KSeF do walidacji, pokazuje status przetworzenia w Odoo, pozwala ręcznie wyeksportować plik XML i domyślnie co trzy godziny pobiera z KSeF faktury kosztowe. Uwierzytelnienie opisano certyfikatem KSeF (plik `.pem`, klucz prywatny i hasło nadane przy wniosku o certyfikat). Firma musi mieć ustawiony kraj Polska i prawidłowy NIP; kontrahenci: kraj i NIP. Źródło: [Odoo 20.0: Poland](https://www.odoo.com/documentation/20.0/applications/finance/fiscal_localizations/poland.html).

Uwierzytelnienie: według Ministerstwa Finansów w KSeF 2.0 działają certyfikaty KSeF (ważne maksymalnie 2 lata), a tokeny zostały utrzymane bezterminowo i działają równolegle z certyfikatami (tokeny z KSeF 1.0 nie działają w KSeF 2.0, trzeba wygenerować nowe). Sposób, który obsługuje dana wersja modułu, sprawdź w dokumentacji tej wersji. Źródło: [ksef.podatki.gov.pl: pytania i odpowiedzi KSeF 2.0](https://ksef.podatki.gov.pl/pytania-i-odpowiedzi-ksef-20/).

Terminy obowiązku KSeF (stan na listopad 2026 r.):

| Od kiedy | Kogo dotyczy |
|---|---|
| 1 lutego 2026 r. | Firmy, których sprzedaż w 2024 r. przekroczyła 200 mln zł (z podatkiem), wystawiają faktury w KSeF; od tego dnia wszyscy podatnicy VAT odbierają faktury przez KSeF. |
| 1 kwietnia 2026 r. | Pozostali podatnicy VAT wystawiają faktury w KSeF. |
| do 31 grudnia 2026 r. | Firmy, których sprzedaż dokumentowana fakturami nie przekracza 10 tys. zł brutto miesięcznie, mogą jeszcze wystawiać faktury poza KSeF, o ile w danym miesiącu nie przekroczą limitu. Co dzieje się po przekroczeniu, sprawdź w objaśnieniach MF przed decyzją. |
| 1 stycznia 2027 r. | Obowiązek obejmuje także te firmy. MF zapowiedziało, że za 2026 r. nie będzie sankcji za błędy w KSeF, więc kary mogą być nakładane od 2027 r. |

Źródła: [ksef.podatki.gov.pl: etapy wdrożenia KSeF](https://ksef.podatki.gov.pl/etapy-wdrozenia-ksef/), [drugi etap wdrożenia KSeF](https://ksef.podatki.gov.pl/wyjasnienia/drugi-etap-wdrozenia-krajowego-systemu-e-faktur/) (próg 200 mln zł, limit 10 tys. zł, sankcje). Przepisy się zmieniają, przed decyzją sprawdź aktualny stan na stronie Ministerstwa Finansów.

Decyzje: który system wystawia fakturę (Odoo czy zewnętrzny program księgowy), kto ma uprawnienia w KSeF i jakim sposobem się uwierzytelnia, jak obsługiwać odrzucenie, ponowienie i korektę, kto dostaje powiadomienie o błędzie, jak wygląda raport uzgodnienia dokumentów i procedura na czas niedostępności KSeF.

Zademonstruj na środowisku testowym KSeF: faktura → wysyłka → potwierdzony status → odrzucenie → poprawa danych → ponowna wysyłka bez duplikatu → korekta → pobranie faktury kosztowej. Na kopii produkcji wyłącz wysyłkę do produkcyjnego KSeF.

Pułapki: oznaczenie „wysłano” traktowane jak „przyjęto” bez potwierdzenia z KSeF; automatyczne ponawianie bez sprawdzenia stanu dokumentu; moduł dodatkowy w Odoo 16–17 bez obsługi aktualnej struktury FA(3); brak właściciela certyfikatu i terminu jego odnowienia.

Pytania do klienta o KSeF: [blok I banku pytań](03-pytania-do-klienta.md#i-ksef-i-fakturowanie-elektroniczne-12-pytań). Scenariusz awarii: AWR-06 *(wkrótce)*. Wdrożenie KSeF w Odoo w praktyce opisuje też wpis [Odoo a KSeF](https://sysmo.pl/odoo-ksef-integracja/) zespołu Sysmo.pl.

## 7. E-commerce

Decyzje: źródło cen, promocji, zapasu, katalogu, zamówień i statusów przesyłek; przepływ płatności; zwroty i reklamacje; SKU/ID.

Zademonstruj: zamówienie z płatnością → rezerwacja → wydanie → kurier → faktura → status w sklepie; następnie anulowanie i zwrot.

Pułapka: dwukierunkowe aktualizowanie tego samego pola w obu systemach bez reguły konfliktów powoduje pętlę synchronizacji.

## 8. HR

Decyzje: zakres danych pracowników, role, nadzór kierowników, reguły akceptacji, retencja, integracja z płacami. Uprawnienia do danych osobowych testuj na kontach ról.

Zademonstruj: utworzenie pracownika → wniosek → zatwierdzenie → zmiana przełożonego → odebranie dostępu po odejściu.

Pułapka: osoby obsługujące nieobecności otrzymują szerszy dostęp do danych kadrowych niż potrzebują.

## 9. Wielospółkowość i uprawnienia: wspólny obowiązek

W każdym obszarze sprawdź użytkownika przypisanego do jednej spółki, wielu spółek i roli z dostępem tylko do odczytu. Testuj rekordy stworzone w każdej spółce i w kontekście domyślnie wybranej firmy. Nie zakładaj, że filtr widoku wystarczy do ochrony danych.

## Akceptacja konfiguracji

Dla każdego obszaru powstaje:

- [ ] Lista decyzji i parametrów, z właścicielem i datą.
- [ ] Zestaw reprezentatywnych rekordów testowych.
- [ ] Co najmniej jeden proces end-to-end z sytuacją błędną.
- [ ] Testy ról, w tym próba niedozwolonej operacji.
- [ ] Lista otwartych GAP i ich wpływu na start.
- [ ] Potwierdzenie finansów/HR/security, jeśli dotyczy.
