# Smart Remote — V12

Poziome wiersze termostatów, bezpośrednia regulacja enkoderem, tęczowy suwak
koloru oraz diagnostyka sprzętowego PWM podświetlenia.

## Pobranie i wgranie

- `smart-remote.yaml` — pełna konfiguracja pilota.
- `txt/smart-remote-v12.txt` — identyczna kopia do kopiowania przez **Raw → Ctrl+A → Ctrl+C**.
- `Instrukcja-Smart-Remote-V12.pdf` — opis obsługi i sprawdzenia PWM.
- `podglad-v12.png` — lista termostatów i wybór koloru; dane przykładowe.

Zastąp całą konfigurację pilota i skompiluj ją z własnymi wpisami Wi-Fi
w `secrets.yaml`. Wystarczy aktualizacja pilota; dotychczasowe pakiety HA
pozostają aktualne. W Statystyki → System po wgraniu zobaczysz **SW: V12**.

## Listy i główny parametr

Listy urządzeń obsługują **strzałki GÓRA/DÓŁ**. Przejście poza ostatni lub
pierwszy wiersz zmienia stronę, więc wszystkie pokoje i urządzenia pozostają dostępne.
**OK** otwiera kartę, **LEWO** wraca. Obracanie enkoderem na liście zachowuje wybór i stronę.

Po wejściu w kartę enkoder od razu reguluje główną nastawę:

- **Lampa:** jasność, również gdy zaznaczona jest ikona żarówki. OK na żarówce
  nadal jest włącznikiem. Krok pozostaje 1% przy wolnym obrocie i 5/10% przy szybkim.
- **Termostat:** temperaturę zadaną, o krok udostępniony przez HA, np. 0,5°C.
- **Roleta:** pozycję, również gdy zaznaczony jest przycisk GÓRA/STOP/DÓŁ.
  Wymagana jest obsługa nastawy pozycji i aktualny odczyt w HA. Przyciski zachowują
  swoje działania; STOP ma dotychczasowy osobny kanał żądania.
- **Gniazdo lub lampa on/off:** pozostaje jawna obsługa OK.

Strzałki w karcie pozwalają wybrać dodatkowy parametr: kolor, nasycenie,
biel, lamele, tryb lub profil. Wtedy enkoder reguluje wybrane pole.
Tryb i profil termostatu nadal zatwierdzasz OK. Utrata HA, brak możliwości danej
encji albo nieaktualne dane blokują odpowiednie sterowanie.

Termostaty są teraz szerokimi poziomymi wierszami, jeden pod drugim, po cztery
na stronę. Lista pokojów nadal pochodzi z HA.

## Tęczowy kolor

Pole **Kolor** ma pełny pasek tęczy i kolorowy znacznik. Położenie oznacza odcień,
a wypełnienie znacznika uwzględnia również aktualne nasycenie.
Brak odczytu daje szary znacznik.

Przy zbliżaniu lub szybkim przejściu do punktu bazowego znacznik lekko się
przyciąga. Punkty są co 60°: czerwony, żółty, zielony, turkus, niebieski i fiolet.
Dokładny punkt dostaje większy znacznik z białą obwódką oraz podpis ze znakiem
**✓**, np. **Zielony ✓**. Kolejny obrót pozwala z niego wyjść.

Znak ✓ potwierdza dokładny odcień w palecie. Nasycenie pozostaje osobnym
parametrem 0–100%, więc pastelowy odcień zachowuje wybrane nasycenie.
Jasność i barwa bieli także są regulowane oddzielnie.

## PWM po usunięciu kondensatora C1

Dla zastosowanego P-FET stan **LOW na GPIO12 włącza podświetlenie**.
Konfiguracja V12 ma jawne **1 kHz**, kanał **0**, zakres **0–100%**,
gamma **1,0** i pojedynczą inwersję zgodną z tym kluczem.
Odpowiada to dotychczasowej częstotliwości i charakterystyce sterowania.

Sprawdzenie kodu FloatOutput i LEDC potwierdza, że zadane **1%** daje około
**1,007% czasu LOW**, czyli **10,07 µs LOW / 989,93 µs HIGH** w okresie 1 ms
przy rozdzielczości 14 bitów. Różnica od 1% wynika z kwantyzacji licznika.

Zgłoszone **1,8 V na pinie podświetlenia ekranu** jest pomiarem za tranzystorem.
Wartość napięcia z miernika nie wyznacza wprost wypełnienia PWM na bramce.
Odczyt rejestrów również nie mierzy napięcia ani prądu diod; pokazuje ustawiony
przebieg sprzętowego generatora.

Pasek **P…%** pokazuje teraz odczyt z LEDC, zamiast oszacowania z LightState.
W **Statystyki → Światło** są dokładniejszy procent ON, procent HIGH na GPIO12
i częstotliwość.

Co 5 s log `backlight` porównuje:

- `target` — nastawę wynikającą z ustawień pilota;
- `state_ON` — bieżącą wartość komponentu światła, także podczas płynnej zmiany;
- `LEDC_ON` / `GPIO12_HIGH` — wartości wynikające z rejestrów LEDC;
- `low_us`, `high_us`, `f`, `bits`, `raw` — czas, częstotliwość, rozdzielczość i licznik;
- `running`, `valid` — pracę kanału i poprawność odczytu.

Przy `running=0` wyjście jest stałe. Czasy LOW/HIGH są wtedy tylko równoważnym
przeliczeniem nominalnego okresu, a nie fizycznymi impulsami. Brak poprawnego
odczytu daje na pasku `P--%`.

### Sprawdzenie na płytce

1. W Konfiguracja → Jasność ustaw **Auto jasność: NIE** i zapisz OK.
2. Ustaw i zapisz **Jasność ręczna: 1%**. Odczekaj zakończenie płynnej zmiany.
3. Sprawdź **LEDC ON** w statystykach lub linię `backlight` w logu.
   Dla 14 bitów oczekiwane jest około **1,01% ON**, **98,99% HIGH**, **1000 Hz**.
4. Do porównania z przebiegiem elektrycznym potrzebny jest pomiar GPIO12;
   pomiar napięcia na wyjściu do ekranu dotyczy innego punktu układu.

## Zachowane funkcje

Podświetlenie nadal czeka na pierwszą pełną klatkę LVGL. Ekran startowy
Home Assistant / Smart Remote / KubAI ma zapisany czas 0–10 s, domyślnie 2 s.
Domyślne czasy mikrofonu, deep sleep i powrotu pozostają **2 / 25 / 10 s**.
Wi-Fi nadal łączy się w tle, a ustawienia i powiązania HA są zachowane.

Końcowy plik przeszedł walidację, kompilację ESP32-S3 oraz testy rzeczywistych
lambd, mapowania PWM, rejestrów, nawigacji, palety i wymiarów 240×320.
Przebieg elektryczny i efekt na fizycznej PCB wymagają potwierdzenia na urządzeniu.
