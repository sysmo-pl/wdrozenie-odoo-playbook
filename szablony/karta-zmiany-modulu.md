# Szablon: karta zmiany / dedykowanego modułu Odoo Python

ID CR/REQ: `[CR-.../REQ-...]` · Nazwa: `[krótka nazwa biznesowa]` · Wersja Odoo / edycja / hosting: `[wartości]`

Właściciel biznesowy: `[rola]` · Tech lead: `[rola]` · Wersja dokumentu: `[v/data]`

## 1. Uzasadnienie customizacji

Problem / cel: `[kto, czego nie może zrobić i jaki jest skutek]`

Dowód FIT/GAP: `[wynik demo standardu i konfiguracji]`

Dlaczego nie wystarcza: `[proces / konfiguracja / Studio / integracja]`

Warianty rozpatrzone: `[A/B/C z kosztem i ryzykiem]`

Decyzja i zatwierdzenie: `[DEC-.../osoba/data]`

## 2. Zakres logiczny

Proces i scenariusze: `[PROC-... / UAT-...]`

Modele / pola / relacje: `[opis bez kodu]`

Reguły walidacji: `[co jest dozwolone i zabronione]`

Role, ACL i record rules: `[wymagania i testy negatywne]`

Multi-company / kontekst spółek: `[reguły]`

Integracje i efekty uboczne: `[co ma wyjść na zewnątrz; atomowość/idempotencja]`

Tłumaczenia i UI: `[ekrany, komunikaty]`

## 3. Wymagania jakościowe

| Aspekt | Kryterium akceptacji |
|---|---|
| ORM / batch | `[wiele rekordów i walidacja bez SQL injection]` |
| Performance | `[wolumen, próg, sposób pomiaru]` |
| Security | `[rola dozwolona/niedozwolona, spółka]` |
| Testy | `[pozytywny, negatywny, wyjątek, współbieżność]` |
| Upgrade | `[obsługiwane wersje, migracja danych, test]` |
| Monitoring | `[co logować bez sekretów i kto reaguje]` |

## 4. Release i utrzymanie

- [ ] Manifest / zależności / instalacja i aktualizacja sprawdzone.
- [ ] Code review oraz testy automatyczne zakończone.
- [ ] ACL, record rules, multi-company, eksport, portal sprawdzone.
- [ ] UAT przez użytkownika biznesowego zaakceptowany.
- [ ] Plan wersji i upgrade opisany; właściciel utrzymania wyznaczony.
- [ ] Dokumentacja administratora, parametrów i rollbacku/kompensacji gotowa.

Zatwierdzenie biznesowe: `[osoba/data]` · Techniczne: `[osoba/data]`

Standard: [dobre praktyki Odoo Python](../docs/08-standard-modulow-python.md)
