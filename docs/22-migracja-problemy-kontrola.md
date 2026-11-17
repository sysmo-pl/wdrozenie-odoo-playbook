# Migracja danych do Odoo: błędy, mapowanie i kontrola zgodności

Migracja nie jest zakończona, gdy import zwróci status „sukces”. Jest zakończona, gdy właściciel danych potwierdzi zgodność liczb, wartości, powiązań, uprawnień oraz zachowania procesów po imporcie.

Materiał uzupełnia [standard migracji](07-migracja-danych.md) i [plan migracji](../szablony/plan-migracji.md). Liczby i wyniki poniżej są fikcyjne.

## Minimalny kontrakt przed migracją

| Pole | Przykład wymaganej decyzji |
|---|---|
| System źródłowy | Jakie rekordy są nadrzędne, a jakie pomocnicze? |
| Właściciel danych | Kto może zaakceptować scalenie duplikatów lub błąd? |
| Klucz obiektu | Jaki stabilny identyfikator przetrwa powtórny import? |
| Mapa transformacji | Waluty, UoM, warianty, stawki, kody krajów, daty, statusy |
| Zakres historyczny | Co przenosimy: master data, otwarte dokumenty, historia? |
| Definicja zgodności | Które liczby, relacje i sumy są uzgadniane? |
| Dry-run | Kiedy, na jakim środowisku, z jaką próbką? |
| Cutover | Kto zamraża źródło, kto liczy deltę, kto podejmuje GO/NO-GO? |
| Rollback | Co można wycofać, a czego nie można bez skutków biznesowych? |

## Pułapka MIG-01: produkt i wariant pomylone podczas mapowania

Objaw: zamówienie dotyczy produktu w rozmiarze M, ale po migracji wskazuje L. Przyczyna do sprawdzenia: mapowanie po etykiecie zamiast po stabilnym identyfikatorze wariantu. Kontrola: mapa źródło → produkt → wariant → jednostka miary, z próbką produktów o podobnych nazwach. **STOP:** choćby jedna nierozstrzygnięta różnica dla aktywnego SKU krytycznego dla produkcji/wysyłki.

## Pułapka MIG-02: dwie osoby/firmy zostały błędnie scalone

Objaw: historia należności lub zgody kontaktowe trafiają do innego kontrahenta. Przyczyna do sprawdzenia: deduplikacja tylko po nazwie, bez NIP/innych identyfikatorów, kraju, roli podmiotu i reguł biznesowych. Kontrola: lista kandydatów do scalenia akceptowana przez właściciela danych; rozdzielenie osoby kontaktowej od firmy; nie zgaduj identyfikacji na podstawie podobnego brzmienia nazwy.

## Pułapka MIG-03: zgodna liczba stanów, błędny magazyn

Objaw: suma ilości jest poprawna, ale partia lub magazyn jest niewłaściwy. Przyczyna: pominięta lokalizacja, partia, własność towaru lub różne definicje „dostępne”. Kontrola: uzgodnienie per produkt × magazyn × lokalizacja × partia (jeśli dotyczy), a nie tylko globalna suma. **STOP:** niezgodność partii regulowanej lub towaru cudzej własności.

## Pułapka MIG-04: otwarte zamówienie zaimportowane jako nowe

Objaw: odtworzone zamówienie ponownie rezerwuje lub fakturuje już częściowo zrealizowaną ilość. Przyczyna: pominięcie ilości już dostarczonej i zafakturowanej, niepoprawna kolejność importu relacji. Kontrola: próbka zamówień otwartych, częściowych, z korektą i anulowanych; potwierdzenie bilansu ilości `zamówiono – dostarczono – anulowano` według definicji klienta.

## Pułapka MIG-05: salda zgodne globalnie, ale nie per konto i spółka

Objaw: suma kontrolna bilansu się zgadza, lecz wartości trafiły do niewłaściwych spółek, walut lub kont. Kontrola: uzgodnienie z księgowością per spółka, waluta, konto i wskazany moment odcięcia; wrażliwe szczegóły finansowe i podatkowe wymagają formalnego potwierdzenia przez osoby uprawnione. **STOP:** niezatwierdzona różnica finansowa.

## Pułapka MIG-06: ponowny import mnoży dane

Objaw: po poprawce mapowania pojawiają się kolejne kontrahenty i produkty. Przyczyna: importer nie rozróżnia `create` od `update` po stabilnym kluczu. Kontrola: dwukrotne wykonanie importu na tej samej próbce; liczba obiektów po drugim przebiegu nie rośnie bez powodu, a aktualizacje zachowują relacje.

## Pułapka MIG-07: zanikają załączniki i uprawnienia

Objaw: dokumenty tekstowe istnieją, ale pliki i dostęp do nich są niepoprawne. Kontrola: losowa i ryzykowna próbka załączników, powiązań i testów roli po odtworzeniu; zweryfikuj filestore oraz retencję.

## Wzór raportu uzgodnienia po dry-run

| Zbiór / kontrola | Źródło | Odoo | Różnica | Status |
|---|---:|---:|---:|---|
| Aktywne SKU | 8 000 | 8 000 | 0 | OK, ale dodatkowo sprawdzić warianty |
| Kontakty po ustalonych regułach scalenia | 4 200 | 4 190 | -10 | OK, pod warunkiem zatwierdzenia listy 10 połączeń |
| Wiersze otwartych zamówień | 12 400 | 12 398 | -2 | BLOKADA: zidentyfikować brakujące pozycje |
| Suma ilości magazynowych (szt.) | 140 500 | 140 500 | 0 | NIEWYSTARCZAJĄCE: nadal zweryfikować lokalizacje/partie |
| Przykładowe salda rozrachunków (PLN) | 520 000,00 | 519 999,98 | -0,02 | Do akceptacji księgowości po analizie przyczyny |

Zasada: „zero różnicy w liczbie rekordów” nie oznacza akceptacji, a tolerancja 0,02 PLN w przykładzie nie jest domyślnie dopuszczalna w rzeczywistym projekcie.

## Testy przed GO

- [ ] Pełny przebieg dry-run powtórzono na aktualnym eksporcie i wersji mapowania.
- [ ] Import można wznowić po awarii bez duplikacji.
- [ ] Właściciele danych zaakceptowali próbki „zwykłe” i „graniczne”.
- [ ] Zgodność została sprawdzona per wymagany wymiar, nie tylko sumarycznie.
- [ ] Przetestowano dokument otwarty, częściowo zrealizowany, z korektą i anulowany.
- [ ] Finanse zatwierdziły wyniki właściwe dla ich obszaru.
- [ ] Zaplanowano delta import, okno cutover i kontrolę zmian po zamrożeniu źródła.
- [ ] Ustalono warunki przerwania i plan bezpiecznego powrotu.
