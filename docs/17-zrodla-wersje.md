# Źródła, kompatybilność wersji i granice materiału

Playbook dotyczy Odoo 16–19 i nowszych; szczegóły techniczne sprawdzono w dokumentacji Odoo 20.0 (wydanej we wrześniu 2026 r.). Najważniejsze różnice między wersjami dotyczą zewnętrznego API (JSON-2 od 19.0, wycofywanie XML-RPC/JSON-RPC), KSeF (standardowy moduł `l10n_pl_edi` od 18.0) i ograniczeń hostingu. Przed decyzją projektową zawsze potwierdź zachowanie w wersji i edycji klienta.

Ostatni przegląd źródeł: 14 listopada 2026 r. Punkt odniesienia: Odoo 20.0. Materiały w repozytorium są niezależnym opracowaniem procedur projektowych, a nie kopią dokumentacji producenta.

## Dokumentacja producenta

| Temat | Dokumentacja Odoo 20.0 | Co sprawdzić w swoim projekcie |
|---|---|---|
| Odoo / aplikacje / administracja | https://www.odoo.com/documentation/20.0/ | Wersja i edycja, na której naprawdę działa klient |
| Backend i moduły | https://www.odoo.com/documentation/20.0/developer/tutorials.html | API frameworka i zależności własnych modułów |
| Coding guidelines | https://www.odoo.com/documentation/20.0/contributing/development/coding_guidelines.html | Styl, struktura, security, wydajność |
| Security / ACL / record rules | https://www.odoo.com/documentation/20.0/developer/reference/backend/security.html | Reguły łączenia grup i testy dostępu |
| Testowanie | https://www.odoo.com/documentation/20.0/developer/reference/backend/testing.html | Framework testowy, klasy i tagi w danym release |
| External JSON-2 | https://www.odoo.com/documentation/20.0/developer/reference/external_api.html | Plan licencyjny, klucze, transakcje, mapowanie API |
| Starsze RPC | https://www.odoo.com/documentation/20.0/developer/reference/external_rpc_api.html | Terminy wycofywania starszych endpointów |
| Hosting | https://www.odoo.com/documentation/20.0/administration/hosting.html | Możliwość własnych modułów dla wybranego modelu hostingu |
| Deployment on-premise | https://www.odoo.com/documentation/20.0/administration/on_premise/deploy.html | Hardening i kopie zapasowe |
| Upgrade | https://www.odoo.com/documentation/20.0/administration/upgrade.html | Plan migracji bazy i kompatybilności modułów |

## Różnice między wersjami Odoo istotne dla wdrożenia

| Temat | Odoo 16–17 | Odoo 18 | Odoo 19 | Odoo 20 (wrzesień 2026) |
|---|---|---|---|---|
| KSeF w lokalizacji polskiej | Brak standardowego modułu: moduł dodatkowy albo aktualizacja do wersji 18 lub nowszej | `l10n_pl_edi` (Community i Enterprise): w aktualnym kodzie serii 18.0; starsze instalacje wymagają aktualizacji kodu | `l10n_pl_edi` | `l10n_pl_edi`, struktura FA(3) |
| Zewnętrzne API | XML-RPC / JSON-RPC | XML-RPC / JSON-RPC | JSON-2 + wycofywane RPC | JSON-2 + wycofywane RPC; usunięta usługa `db` |
| Odoo Studio | Tylko Enterprise | Tylko Enterprise | Tylko Enterprise | Tylko Enterprise |
| Własne moduły Python | Odoo.sh, on-premise; nie Odoo Online | jw. | jw. | jw. |

Usunięcie usług `common` i `object` XML-RPC/JSON-RPC producent zapowiada na Odoo 22 (jesień 2028). Źródła: [External JSON-2 API (20.0)](https://www.odoo.com/documentation/20.0/developer/reference/external_api.html), [Poland (20.0)](https://www.odoo.com/documentation/20.0/applications/finance/fiscal_localizations/poland.html), [Odoo 20 release notes](https://www.odoo.com/odoo-20-release-notes). Moduł `l10n_pl_edi` opisuje też [dokumentacja 18.0](https://www.odoo.com/documentation/18.0/applications/finance/fiscal_localizations/poland.html).

## Trzy istotne zastrzeżenia wersji

1. Moduły Python: nie zakładaj możliwości własnego modułu na Odoo Online; Odoo.sh i instalacje własne pozwalają na inny model zarządzania rozszerzeniami. Zawsze sprawdź bieżący hosting i ofertę.
2. API zewnętrzne: JSON-2 API (od 19.0) to punkt odniesienia dla nowych integracji. Dokumentacja 20.0 ogranicza dostęp do zewnętrznego API do planów Custom. XML-RPC / JSON-RPC są wycofywane, nie opieraj nowego projektu na nieaktualnym poradniku.
3. Aktualizacje: upgrade danych nie oznacza automatycznej kompatybilności własnych modułów; każdy upgrade wymaga oddzielnego testu customizacji, ACL, integracji i krytycznych procesów.

## Źródła bezpieczeństwa i prywatności

- [OWASP ASVS](https://asvs.owasp.org/), zestaw obszarów weryfikacji bezpieczeństwa aplikacji.
- [OWASP Secrets Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html), praktyki zarządzania sekretami.
- [EDPB](https://www.edpb.europa.eu/), materiały dotyczące ochrony danych osobowych w UE.
- [Ministerstwo Finansów: KSeF](https://ksef.podatki.gov.pl/), bieżące źródło zasad dotyczących KSeF. Terminy podane w sekcji [KSeF w Odoo](06-konfiguracja-obszarow.md#6-ksef-w-odoo) są oznaczone datą stanu i wymagają okresowej weryfikacji.

## Jak aktualizować tę bazę wiedzy

Przy major upgrade Odoo: przegląd techniczny sekcji Python/API/bezpieczeństwo/testy; po zmianach w prawie podatkowym: przegląd fragmentów finansowych i KSeF; po incydencie wdrożeniowym: uzupełnij scenariusz bez danych klienta. W pull request wpisz: wersja, co się zmieniło, skutek dla istniejących wdrożeń, link do źródła.

Znak towarowy: Odoo jest znakiem towarowym Odoo S.A. Linki do dokumentacji służą jako źródła i nie oznaczają zatwierdzenia materiału przez Odoo S.A.
