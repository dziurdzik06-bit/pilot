# Smart Remote — V10: spokojny start ekranu

Poprawka krótkiego szarego szumu widocznego przed ekranem powitalnym V9.
Podświetlenie pozostaje wyłączone, dopóki LVGL nie wyśle całej pierwszej klatki.
Wtedy włącza się z zapisaną jasnością ręczną albo automatyczną.

## Wgranie

Wgraj pełny `smart-remote.yaml` lub identyczną zawartość
`txt/smart-remote-v10.txt` jako konfigurację pilota w ESPHome.
Własne SSID i hasło pozostają w `secrets.yaml`.

Na GitHubie wybierz **Raw** w pliku TXT, następnie **Ctrl+A → Ctrl+C**.
Możesz też pobrać repozytorium przez **Code → Download ZIP** i użyć folderu `v10`.
Wystarczy aktualizacja pilota; pakiety HA pozostają zgodne z V8.

## Kolejność startu

1. Wyjście podświetlenia ustawia klucz w stan wyłączony przed zasileniem peryferiów.
2. Kontroler LCD i LVGL przygotowują pełny obraz logo lub menu.
3. Po zakończeniu rzeczywistego rysowania i transmisji SPI podświetlenie zostaje odblokowane.
4. Dopiero wtedy zaczyna biec ustawiony czas logo, domyślnie **2 s**.

Samo puste odświeżenie LVGL nie zapala podświetlenia. Nie jest używane sztywne
opóźnienie przed jego włączeniem. Jeśli pierwsza klatka nie powstanie, światło
pozostaje wyłączone, a oczekiwanie na ekran powitalny jest ograniczone, aby
urządzenie mogło normalnie przejść w deep sleep.

Przed uśpieniem kod bezpośrednio wyłącza PWM podświetlenia, a następnie
odcina zasilanie peryferiów.

Logo Home Assistant, **Smart Remote**, **KubAI** i ustawienie
**Ustawienia → Konfiguracja → Czasy → Ekran startowy** pozostają takie jak w V9:
zakres **0–10 s**, `0` wyłącza logo. Wi-Fi nadal łączy się w tle.
Zachowane są termostaty, lampy, rolety, gniazda, IR, czujniki i test mikrofonu.

## Sprawdzenie na PCB

Po wgraniu sprawdź wybudzenie z deep sleep oraz zwykłe włączenie zasilania.
Oczekiwany efekt to krótki ciemny ekran, a następnie gotowe logo lub menu.

Poprawkę sprawdzono testami rzeczywistych lambd C++, walidacją i kompilacją
dla ESP32-S3. Efekt optyczny wymaga potwierdzenia na fizycznej PCB.
Kondensator C1 za kluczem podświetlenia utrzymuje zgromadzony ładunek;
jeżeli mimo poprawki pozostanie błysk jeszcze przed startem firmware,
trzeba będzie sprawdzić jego rozładowanie i stan klucza podczas resetu.
