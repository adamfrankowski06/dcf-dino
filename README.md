# Wycena DCF — Dino Polska (GPW: DNP)

Pierwszy projekt portfolio z zakresu modelowania finansowego — pełna wycena metodą DCF (Discounted Cash Flow) dla Dino Polska S.A., zbudowana od zera w Pythonie na podstawie realnych, publicznie dostępnych sprawozdań finansowych spółki.

## Kontekst

To mój pierwszy projekt tego typu, i nie traktuję go jako coś imponującego — traktuję go jako pierwszy krok. Dopiero zaczynam studia na kierunku Finanse i Rachunkowość, a większość materiału potrzebnego do zbudowania tego modelu (sprawozdania finansowe, wartość pieniądza w czasie, WACC, FCF, wycena DCF) przerobiłem samodzielnie, zanim jeszcze usiadłem do pierwszych zajęć.

Model budowałem z pomocą AI (Claude) jako korepetytora — tłumaczącego koncepcje od zera, sprawdzającego moje obliczenia i łapiącego błędy po drodze — ale każdą linijkę kodu pisałem, uruchamiałem i poprawiałem sam. Ten projekt to dla mnie punkt startowy do nauki modelowania finansowego, nie jej efekt końcowy — i pewnie za pół roku spojrzę na niego i zobaczę mnóstwo rzeczy do poprawienia. Tak to ma zresztą wyglądać.

## O projekcie

Celem było przejście przez cały proces wyceny DCF krok po kroku: od historycznych danych finansowych, przez prognozę przyszłych przepływów pieniężnych, aż po konkretną wycenę jednej akcji — i porównanie jej z rzeczywistą ceną rynkową.

Projekt powstał jako część nauki podstaw finansów i rachunkowości, z naciskiem na zrozumienie **dlaczego** model liczy to, co liczy, a nie tylko na mechaniczne odtworzenie wzorów.

## Dane źródłowe

- Skonsolidowane sprawozdanie finansowe Dino Polska S.A. za rok 2025 (grupadino.pl, sekcja dla inwestorów)
- Dane rynkowe (cena akcji, rentowność obligacji skarbowych, beta, equity risk premium) — zebrane we wrześniu 2026

## Metodologia

1. **Dane historyczne** — wczytanie RZiS i rachunku przepływów pieniężnych za 2025 rok do pandas, policzenie rzeczywistego FCF za 2025 (566 010,88 tys. zł), zweryfikowanego przez rekoncyliację EBITDA i porównanie efektywnej stopy podatkowej ze stawką ustawową CIT (19%).
2. **Prognoza przychodów i FCF (2026–2030)** — tempo wzrostu przychodu malejące liniowo z 13% do 2% w ciągu 5 lat; koszty, amortyzacja i kapitał obrotowy prognozowane jako procent przychodu, na bazie wskaźników z 2025 roku.
3. **WACC** — średni ważony koszt kapitału policzony z realnej struktury finansowania spółki (kapitalizacja rynkowa vs dług oprocentowany) oraz kosztu kapitału własnego metodą CAPM.
4. **Dyskontowanie** — sprowadzenie prognozowanych FCF do wartości dzisiejszej stopą WACC.
5. **Wartość rezydualna** — wzór Gordona, zakładający stabilny wzrost 2,5% w nieskończoność po roku 2030.
6. **Wycena końcowa** — Enterprise Value → Equity Value (po odjęciu długu netto) → cena za akcję, porównana z ceną rynkową.

## Kluczowe założenia

| Parametr | Wartość |
|---|---|
| Tempo wzrostu przychodu (2026→2030) | 13% → 2% (liniowo) |
| Wzrost rezydualny (g) | 2,5% |
| Stopa wolna od ryzyka (rentowność obligacji PL 10L) | 6,30% |
| Beta (Dino Polska) | 0,50 |
| Equity risk premium (Polska) | 5,33% |
| Koszt kapitału własnego (CAPM) | 8,965% |
| Koszt długu (przed opodatkowaniem, założenie) | 7,5% |
| Efektywna stopa podatkowa | 19,09% |
| WACC | 8,92% |
| Struktura kapitału (kapitał własny / dług) | 98,35% / 1,65% |

## Wyniki — dwie wersje modelu

Model policzony jest w dwóch wariantach, żeby pokazać jak bardzo wrażliwa jest wycena DCF na założenia dotyczące nakładów inwestycyjnych (Capex):

| Wersja | Założenie o Capeksie | Cena za akcję | vs cena rynkowa (35,48 zł) |
|---|---|---|---|
| **V1 — bazowa** | Stały poziom 6,2% przychodu przez cały okres prognozy i w nieskończoność | 4,32 zł | ~12% |
| **V2 — poprawiona** | Capex maleje liniowo z 6,2% do 1,5% przychodu (poziom amortyzacji) wraz ze spadającym tempem wzrostu | 32,35 zł | ~91% |

### Najważniejszy wniosek projektu

Wersja 1 zaniżała wycenę niemal 8-krotnie względem rynku. Powód: trzymanie Capeksu na poziomie odpowiadającym agresywnej ekspansji (6,2% przychodu) nawet w fazie, gdy model zakłada już tylko stabilny, niski wzrost (2,5%) — co jest ekonomicznie niespójne. Skoro wartość rezydualna odpowiada za większość całej wyceny, ten jeden błąd założenia miał ogromny wpływ na wynik końcowy.

Poprawka — pozwolenie Capeksowi zjechać w kierunku poziomu "utrzymaniowego" (zbliżonego do amortyzacji) w miarę jak tempo wzrostu wygasa — przybliżyła wycenę do ceny rynkowej z ~12% do ~91%.

**Wniosek nie jest taki, że V2 jest "prawdziwa", a V1 "błędna"** — obie są funkcją przyjętych założeń. Pokazuje to jednak, jak bardzo modele DCF są wrażliwe na pozornie drugorzędne szczegóły, i dlaczego każde założenie w takim modelu powinno być jawnie uzasadnione, nie ukryte jako "twardy fakt".

## Ograniczenia modelu

- Koszt długu (7,5%) to założenie rynkowe, nie liczba wprost odczytana ze sprawozdania — nie znaleziono jasno podanej stopy procentowej kredytów Dino, a że dług stanowi ~1,65% finansowania spółki, precyzja tego założenia ma minimalny wpływ na wynik.
- Prognoza opiera się na jednym scenariuszu wzrostu — model nie zawiera analizy wrażliwości ani scenariuszy (optymistyczny/pesymistyczny).
- Marża operacyjna, procent amortyzacji i procent kapitału obrotowego trzymane są na stałym poziomie z 2025 roku (poza Capeksem w V2).

## Zastrzeżenie

Projekt ma charakter edukacyjny. Nie stanowi rekomendacji inwestycyjnej. Autor nie jest doradcą finansowym, a wynik modelu jest w pełni zależny od przyjętych założeń.

## Technologie

Python, pandas, numpy — Jupyter / VS Code

## Autor

Adam — projekt zrealizowany w ramach samodzielnej nauki podstaw finansów i rachunkowości, wrzesień 2026.
