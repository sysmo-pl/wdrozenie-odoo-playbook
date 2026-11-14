# Playbook wdrożeń Odoo

Metodyka, pytania do klienta i szablony dokumentów, których używamy przy wdrożeniach Odoo ERP: od pierwszej rozmowy i warsztatów discovery, przez konfigurację, moduły w Pythonie, integracje i migrację danych, po odbiór, uruchomienie i utrzymanie. Materiał jest dla kierowników projektów, konsultantów, programistów i osób po stronie klienta, które odbierają wdrożenie.

Jeśli nie wiesz, od czego zacząć, otwórz [START-HERE](START-HERE.md).

## Zawartość

- [Spis rozdziałów](docs/README.md), m.in. [etapy wdrożenia](docs/01-etapy-wdrozenia.md), [pytania do klienta](docs/03-pytania-do-klienta.md), [FIT/GAP](docs/04-mapa-procesow-fit-gap.md), moduły Python *(wkrótce)*, integracje API *(wkrótce)*, migracja danych *(wkrótce)*, Go-Live *(wkrótce)*, [KSeF w Odoo](docs/06-konfiguracja-obszarow.md#6-ksef-w-odoo) i FAQ *(wkrótce)*
- [Szablony](szablony/README.md): karta projektu, karta procesu, rejestr wymagań i rejestr ryzyk (kolejne wkrótce)
- Przykłady *(wkrótce)*: sześć scenariuszy do warsztatów; firmy, liczby i dokumenty są w nich zmyślone (syntetyczne), nie pochodzą od klientów
- [Słownik pojęć](docs/16-slownik.md)

Repozytorium nie zawiera kodu ani danych klientów. Rozdziały publikujemy stopniowo; te oznaczone „wkrótce” dojdą w kolejnych wydaniach (zob. [CHANGELOG](CHANGELOG.md)).

## Wersje Odoo

Odoo 16–19 i nowsze, w tym 20, Community i Enterprise. Szczegóły techniczne sprawdzaliśmy w dokumentacji Odoo 20.0; różnice między wersjami (API, KSeF, hosting) są w [tabeli wersji](docs/17-zrodla-wersje.md#różnice-między-wersjami-odoo-istotne-dla-wdrożenia). API, uprawnienia i możliwości Studio trzeba potwierdzić dla konkretnej instalacji. To nie jest porada prawna, księgowa ani podatkowa.

## Licencja

Wersja 1.0.0, aktualizacja 14 listopada 2026 r. Treść: [CC BY 4.0](LICENCJA.md). Historia zmian: [CHANGELOG](CHANGELOG.md), cytowanie: [CITATION.cff](CITATION.cff). Zgłoszenia: [SECURITY](SECURITY.md), [CONTRIBUTING](CONTRIBUTING.md).

Odoo jest znakiem towarowym Odoo S.A. To niezależne opracowanie, a nie oficjalna dokumentacja Odoo.

## Sysmo.pl

Playbook przygotował zespół [Sysmo.pl](https://sysmo.pl/). Wdrażamy i rozwijamy Odoo, piszemy moduły dedykowane, prowadzimy migracje i integracje, w tym z KSeF; kod i repozytorium zostają u klienta. Więcej: [wdrożenie i modyfikacje Odoo ERP](https://sysmo.pl/wdrozenie-modyfikacje-odoo-erp-polska/).

## English summary

A Polish-language playbook for Odoo ERP implementations (versions 16–20): discovery questions, FIT/GAP, custom Python modules, API integrations, data migration, UAT, go-live and KSeF e-invoicing. Text licensed under CC BY 4.0; not official Odoo documentation. Published in stages.
