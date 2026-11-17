# Etapy wdrożenia Odoo ERP od 0 do 10

Wdrożenie Odoo ERP przebiega w 11 etapach: od kwalifikacji (0) przez kick-off, discovery, FIT/GAP, projekt rozwiązania, realizację, migrację i integracje, UAT, Go-Live i okres wzmożonego wsparcia (hypercare) do utrzymania i rozwoju (10). Każdy etap kończy się bramką: warunkiem, bez którego nie przechodzi się dalej.

Kolejność jest orientacyjna, to nie sztywny waterfall. Część prac (dostępy do API, dane do migracji, role) zaczyna się już w discovery.

## Mapa decyzyjna

| Etap | Wejście | Działania obowiązkowe | Dokument / dowód | Bramka (gate): warunek przejścia |
|---|---|---|---|---|
| 0. Kwalifikacja | Potrzeba klienta | Cele, skala, systemy, termin, wstępna ocena wykonalności | Karta projektu v0 | Sponsor i orientacyjny zakres potwierdzeni |
| 1. Kick-off | Decyzja o projekcie | Role, RACI, komunikacja, plan warsztatów, dane/dostępy | Protokół kick-off | Znamy osoby decyzyjne i plan discovery |
| 2. Discovery | Procesy i interesariusze | Wywiady, demonstracje AS-IS, wolumeny, wyjątki | Karty procesów, wymagania | Klient potwierdził obraz procesów i luki wiedzy |
| 3. FIT/GAP | Zweryfikowane wymagania | Demo standardu, warianty, estymacje, priorytety | Backlog FIT/GAP, decyzje | Zakres MVP i CR zatwierdzone |
| 4. Projekt | Zatwierdzony zakres | Hosting, edycja, moduły, role, dane, integracje, bezpieczeństwo | Projekt rozwiązania i plan testów | Przegląd architektury + akceptacja kosztów/licencji |
| 5. Realizacja | Projekt techniczny | Konfiguracja, rozszerzenia, demo, review, testy modułowe | Działający staging, wyniki testów | Kryteria zadań spełnione; brak krytycznych luk |
| 6. Migracja i integracje | Model danych, środowiska | Dry-run, mapowanie, kontrakty API, próby odtwarzania | Raport uzgodnienia danych, monitoring | Dane i przepływy uzgodnione |
| 7. UAT i szkolenia | Scenariusze na staging | Testy E2E, role negatywne, wyjątki, instrukcje użytkownika | Raport UAT i odbiór biznesowy | Krytyczne scenariusze zaakceptowane |
| 8. Go-Live | Przegląd gotowości | Cutover, backup, migracja finalna, smoke test, decyzja GO | Protokół Go-Live | Zgoda sponsora i właścicieli procesów |
| 9. Hypercare | System produkcyjny | Dyżury, triage, szybkie poprawki, codzienne uzgodnienia | Rejestr incydentów, status stabilizacji | Wskaźniki i incydenty w uzgodnionych granicach |
| 10. Rozwój | Stabilne operacje | Retrospektywa, backlog usprawnień, aktualizacje | Plan rozwoju i utrzymania | Właściciel systemu przejmuje odpowiedzialność |

## Etap 0: kwalifikacja i decyzja „czy Odoo?”

Rozmowa: „Jaki konkretny problem biznesowy ma rozwiązać zmiana systemu i jak poznamy po 3 miesiącach, że się udało?”

1. Ustal cel i 2–5 mierników, np. skrócenie czasu od zamówienia do wysyłki, zmniejszenie błędów stanów, czas zamknięcia miesiąca.
2. Policz użytkowników w rolach, firmy w grupie, magazyny, produkty, zamówienia/dzień, integracje i języki.
3. Sprawdź ograniczenia: termin wynikający z prawa, bramki finansowe, dostępność ludzi i jakości danych.
4. Zapisz warianty: Odoo standard, Odoo z rozszerzeniem, inny produkt / brak wdrożenia.
5. Nie wyceniaj customizacji na podstawie jednego zrzutu ekranu; oznacz luki jako wymagające warsztatu.

No-go: brak sponsora, brak celów, całkowicie nieznany stan danych, termin bez uzasadnionego planu lub krytyczny wymóg nieosiągalny na proponowanym hostingu.

## Etap 1: Kick-off

1. Uzgodnij granice projektu: organizacje, procesy, kanały sprzedaży, lokalizacje, systemy, które pozostają.
2. Wyznacz RACI, właściciela architektury, właścicieli danych, key users i osoby zatwierdzające księgowość.
3. Uzgodnij format statusów, miejsce dokumentów i ścieżkę decyzji / change request.
4. Zaplanuj warsztaty według procesów end-to-end, a nie tylko według ekranów Odoo.
5. Wprowadź rejestr ryzyk i decyzji od pierwszego dnia; termin odpowiedzi na otwarte pytania musi być znany.

Bramka: sponsor, właściciele procesów i odpowiedzialności znane; zaplanowane warsztaty oraz zasady akceptacji.

## Etap 2: Discovery i analiza AS-IS

1. Rozpocznij warsztat od rzeczywistego zamówienia/dokumentu z usuniętymi danymi wrażliwymi.
2. Poproś key usera, aby pokazał, jak praca wygląda dziś, także na Excelach i poza systemem.
3. Zapisz zdarzenia startu/końca, role, system źródłowy i system docelowy, decyzje, wyjątki i walidacje.
4. Zbieraj liczby: średnie/szczytowe wolumeny, długość dokumentów, liczba jednostek, częstotliwość korekt.
5. Rozdziel wymaganie („nie można wydać towaru bez akceptacji kredytowej”) od pomysłu na UI („chcemy przycisk”).
6. Każde wymaganie otrzymuje testowalne kryterium akceptacji.

Bramka: właściciel procesu zweryfikował schemat AS-IS i listę krytycznych wyjątków; niewiadome są jawne.

## Etap 3: FIT/GAP i zakres MVP

1. Dla każdego `REQ` pokaż standard Odoo na danych demonstracyjnych.
2. Oznacz: FIT standard, FIT konfiguracja, FIT Studio, GAP moduł, GAP integracja, zmiana procesu, odrzucone / odłożone.
3. Dla luki porównaj warianty pod kątem bezpieczeństwa, kosztu, czasu, wydajności i kolejnego upgrade'u.
4. Przypisz priorytet: Must / Should / Could / Won't z uzasadnieniem biznesowym.
5. Doprecyzuj granice fazy 1: jakie kanały, magazyny, raporty i dane startują, a jakie później.
6. Potwierdź na piśmie kryteria odbioru i procedurę zmian zakresu.

Bramka: zatwierdzony backlog MVP, koszt i wpływ customizacji, lista zagadnień poza zakresem.

## Etap 4: Projekt rozwiązania

1. Zatwierdź edycję i hosting na podstawie wymagań dla niestandardowych modułów i zewnętrznego API.
2. Zaprojektuj segregację środowisk DEV/STAGING/PROD, kont, danych i uprawnień.
3. Zaprojektuj master data: produkty, kontrahenci, konta, podatki, jednostki, magazyny i jednoznaczne identyfikatory.
4. Dla każdej integracji opisz: source of truth, właściciela, zdarzenie, kontrakt, retry, duplikaty, monitoring, awarie.
5. Ustal wymogi RODO, retencję, kopie zapasowe i odtwarzanie z rzeczywistym testem restore.
6. Zaplanuj UAT: minimum pełne ścieżki biznesowe i testy ról, nie same formularze.

Bramka: tech lead i właściciel biznesowy akceptują projekt, ograniczenia i kryteria bezpieczeństwa.

## Etap 5: Konfiguracja i development

1. Buduj w małych pionowych przekrojach procesu: np. oferta → zamówienie → rezerwacja → dostawa → faktura.
2. W każdym przekroju uzupełniaj role, walidacje, dane testowe, logi i scenariusz błędu.
3. Każdą zmianę modułu sprawdź w code review i testach automatycznych odpowiednich dla wersji Odoo.
4. Demo kończ decyzją: akceptujemy, zgłaszamy defekt lub tworzymy CR.
5. Nie pozwalaj, aby nowe wymagania spoza zakresu „wchodziły przy okazji” bez analizy wpływu.

Bramka: zaakceptowane wymagania i kryteria Done; brak otwartych usterek bezpieczeństwa i integralności w danym zakresie.

## Etap 6: Integracje i migracja danych

1. Wykonaj co najmniej próbę migracji reprezentatywnej próbki, a potem próbę pełną.
2. Sprawdź duplikaty, relacje, podatki, stany, numery dokumentów, załączniki i mapowanie ID.
3. Uzgodnij sumy, liczności i salda z właścicielami danych; nie „zatwierdzaj przez IT”.
4. W integracji zasymuluj: timeout po zapisie, ponowną dostawę zdarzenia, brak odpowiedzi API, zmianę schematu, awarię kolejki.
5. Zaplanuj cutover: okno zamrożenia, delta danych, ostatnia kopia, weryfikacja i plan awaryjny.

Bramka: protokoły uzgodnienia danych i testów przepływów, a dla każdej niezgodności właściciel i plan.

## Etap 7: UAT, szkolenia i formalny odbiór

1. Przekaż scenariusze na czystych, reprezentatywnych danych testowych.
2. Użytkownicy z właściwymi rolami wykonują testy; konsultant nie zastępuje ich w podpisywaniu odbioru.
3. Sprawdź ścieżki pozytywne, negatywne, anulowania/korekty i sytuacje „poza standardowym scenariuszem”.
4. Zbierz dowody wykonania (ID testu, wynik, screenshot bez sekretów, defekt, poprawka, retest).
5. Przeszkol użytkowników na procesach, które realnie wykonują; określ, kto ich wspiera w pierwszym tygodniu.

Bramka: testy P0/P1 zakończone sukcesem, uzgodniony rejestr wyjątków i akceptacja biznesu.

## Etap 8: Go-Live

1. Przeprowadź próbę generalną i przegląd listy kontrolnej T-7 i T-1.
2. Wyznacz osobę podejmującą decyzję GO/NO-GO i próg zatrzymania.
3. Potwierdź wykonanie i możliwość przywrócenia backupu; zsynchronizuj integracje i konta techniczne.
4. Zrób finalną migrację, uzgodnienia, smoke test i otwarcie użytkownikom.
5. Każda zmiana po starcie przechodzi proces awaryjny / change management.

Bramka: podpisany protokół uruchomienia lub decyzja o powrocie, z czasem i właścicielem.

## Etap 9: Hypercare

1. Uzgodnij kanał zgłoszeń, okno dyżuru, priorytety i osoby pierwszej linii.
2. Codziennie przeglądaj niedostarczone integracje, błędy migracji, zamówienia/faktury w stanie pośrednim.
3. Rozdzielaj błędy systemu od luk szkoleniowych i zmian zakresu.
4. Prowadź krótkie raporty stabilności: incydenty otwarte, czas obejścia, stan kolejek, błędy użytkowników.
5. Zaplanuj przekazanie do utrzymania z runbookiem i właścicielami.

Bramka: ustalone metryki stabilności spełnione, otwarte problemy mają plan i termin.

## Etap 10: Utrzymanie i rozwój

1. Przekaż listę modułów, integracji, kont, harmonogramów, backupów i właścicieli.
2. Mierz bazowe KPI biznesowe ustalone w etapie 0.
3. Przygotuj kalendarz patchy i aktualizacji Odoo, zwłaszcza dla custom modułów i zewnętrznych API.
4. Rozwijaj backlog, ale nie łącz zmian funkcjonalnych z awaryjnymi bez kontroli ryzyka.
5. Co jakiś czas ponownie przeprowadź review uprawnień, rotację kluczy i test restore.

## Reguła blokady Go-Live

**Nie rekomenduj GO**, jeżeli: nie da się odtworzyć danych; test uprawnień ujawnia informacje nieuprawnionym rolom; nie uzgodniono danych krytycznych; kluczowa integracja gubi albo dubluje transakcje; brak właściciela decyzji lub obejścia dla procesu krytycznego.

Weryfikacja procesu: [drzewo decyzji customizacji](19-drzewo-decyzyjne-customizacja.md) · katalog błędów *(wkrótce)* · audyt po wdrożeniu *(wkrótce)*.
