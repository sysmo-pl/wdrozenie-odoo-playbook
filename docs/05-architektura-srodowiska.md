# Architektura Odoo: edycja, hosting, środowiska i decyzje techniczne

Architekturę Odoo zaczyna się od pytań o własne moduły Python i zewnętrzne API, a nie od ceny serwera. Odoo Online nie obsługuje niestandardowych aplikacji; Odoo.sh i instalacja własna (on-premise) tak. Projekt obejmuje też oddzielne środowiska DEV/STAGING/PROD, ścieżkę zmian oraz kopie zapasowe z udokumentowanym testem odtworzenia (RPO/RTO).

Kiedy: po wstępnym FIT/GAP, przed rozpoczęciem developmentu i ustaleniem migracji. Właściciel: architekt / tech lead, z zatwierdzeniem właściciela biznesowego i osoby odpowiedzialnej za IT klienta.

## 1. Edycja i hosting: nie zaczynaj od ceny serwera

Najpierw odpowiedz na cztery pytania: Czy potrzebny jest własny moduł Python? Czy potrzebna jest zewnętrzna integracja API? Jakie są wymogi danych, aktualizacji i backupu? Czy moduły/rozszerzenia są dostępne na wybranej edycji i planie?

| Wariant | Typowe zalety | Krytyczne ograniczenia do sprawdzenia |
|---|---|---|
| Odoo Online (SaaS) | Utrzymanie platformy po stronie producenta | Brak własnych modułów Python i aplikacji spoza standardu; zewnętrzne API tylko w planie Custom; ograniczona administracja |
| Odoo.sh (PaaS producenta) | Własne moduły z repozytorium Git, środowiska deweloperskie i testowe | Koszt, ograniczenia i odpowiedzialności operacyjne; licencja, plan aktualizacji |
| On-premise / hosting własny | Kontrola infrastruktury, modułów i integracji | Pełna odpowiedzialność za konfigurację, patching, backup, monitoring, bezpieczeństwo |

**Uwaga wersji:** dokumentacja Odoo 20.0 stwierdza, że Odoo Online nie jest kompatybilne z niestandardowymi aplikacjami. Według tej samej dokumentacji dostęp do danych przez zewnętrzne API mają tylko plany cenowe Custom (nie One App Free ani Standard). Odoo Studio jest aplikacją edycji Enterprise, w Community go nie ma. Ofertę i plany potwierdź przed podpisaniem architektury. Źródła: [hosting Odoo 20](https://www.odoo.com/documentation/20.0/administration/hosting.html), [JSON-2 Odoo 20](https://www.odoo.com/documentation/20.0/developer/reference/external_api.html).

## 2. Rejestr decyzji architektonicznej (ADR)

Każda istotna decyzja powinna mieć: kontekst → warianty → decyzję → konsekwencje → właściciela → datę ponownego przeglądu. Przykład: „Odrzucamy Odoo Online, ponieważ wymagamy własnych modułów Pythona. Przyjmujemy Odoo.sh, pod warunkiem potwierdzenia planu, kosztu i procesu deploymentu”.

Najczęstsze ADR-y: hosting, edycja, wersja bazowa, liczba baz i spółek, lokalizacja danych, mechanizm integracji, strategia migracji, środowiska, kolejki, monitoring, mechanizm backup/restore, model autoryzacji.

## 3. DEV / STAGING / PROD

| Środowisko | Co wolno robić | Czego nie robić |
|---|---|---|
| DEV | Development, testy, migracje próbne, eksperymenty | Używać niezanonimizowanej kopii produkcji bez podstawy i zabezpieczeń |
| STAGING | Próby zbliżone do produkcji, UAT, testy integracji i release candidate | Przypadkowo wysyłać prawdziwe maile, płatności, KSeF lub zlecenia do kuriera |
| PROD | Operacje biznesowe i kontrolowane wdrożenia | Bezpośrednio poprawiać kod lub dane bez procedury, logu i testu |

Na stagingu wyłącz lub przekieruj na sandbox: e-maile, SMS, płatności, webhooki wychodzące, KSeF, wysyłki i zadania cykliczne. Kopie bazy z produkcji neutralizuj i minimalizuj; zależnie od modelu hostingu sprawdź mechanizmy producenta. Dla każdej integracji przechowuj osobne klucze na środowiska.

## 4. Ścieżka zmian

1. Wymaganie z testem i zgodą biznesową.
2. Zmiana w odizolowanym środowisku / gałęzi (kod pozostaje prywatny).
3. Przegląd funkcjonalny, bezpieczeństwa, wielospółkowości i wydajności.
4. Testy automatyczne oraz migracyjne, gdy zmienia się schemat danych.
5. Test na stagingu i akceptacja release candidate.
6. Backup / plan powrotu / komunikat o wdrożeniu.
7. Wdrożenie w kontrolowanym oknie, smoke test, obserwacja i wpis do changelogu.

Nie utożsamiaj rollbacku aplikacji z rollbackiem danych: po zmianie schematu lub wystawieniu dokumentów nie zawsze da się bezpiecznie wycofać wersję przez zwykłe przełączenie gałęzi.

## 5. Backup / restore / RPO / RTO

- RPO: jaką maksymalną utratę danych w czasie biznes akceptuje. Nie jest liczbą „jeden dzień”, bo robimy backup raz dziennie: musi wynikać z procesu.
- RTO: jak długo może trwać odtworzenie kompletnej usługi.
- Backup musi uwzględniać bazę danych, filestore/załączniki, klucze niezbędne do odtworzenia konfiguracji oraz zależne zasoby odpowiednio do hostingu.
- Trzymaj kopię poza tym samym punktem awarii; ogranicz dostęp do kopii i zaplanuj ich retencję.
- Wykonuj regularny próbny restore na odizolowanym środowisku, z testem czy da się otworzyć załącznik, zalogować i odtworzyć ważny proces.
- Zapisz odpowiedzialność: kto monitoruje backupy, kto potwierdza wynik, kto podejmuje decyzję o odtworzeniu.

## 6. Dodatkowe decyzje techniczne

- Wielospółkowość: zdefiniuj firmy, reguły współdzielenia kontrahentów i produktów, raportowanie i rozdzielenie danych.
- Wydajność: określ ładunki godzinowe, ciężkie widoki, raporty, ilość rekordów i akceptowalny czas odpowiedzi.
- Niezawodność: ustal, co działa bez integracji; czy zamówienia są blokowane, kolejkowane lub obsługiwane ręcznie.
- Dane: źródło prawdy, identyfikatory techniczne, jakość i właściciele.
- Compliance: dokumentuj wymagania klientów i regionów; technologia nie może zgadywać zasad księgowych lub prawnych.

## Bramka: gotowość architektury

- [ ] Wersja, edycja, hosting i plan licencyjny potwierdzone względem wymagań.
- [ ] Własne moduły i integracje są technicznie możliwe na wybranym hostingu.
- [ ] Odtwarzanie środowiska, RPO/RTO i odpowiedzialność za backup są opisane.
- [ ] Istnieją oddzielne DEV/STAGING/PROD i zasada anonimizacji danych testowych.
- [ ] Wszystkie interfejsy mają właścicieli i strategię błędów.
- [ ] Kryteria bezpieczeństwa i aktualizacji są akceptowane.

Dalej: [moduły Python](08-standard-modulow-python.md) · [API](09-standard-integracji-api.md) · [security](10-bezpieczenstwo.md)
