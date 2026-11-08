# Smart Remote — V9: ekran powitalny

Po włączeniu lub wybudzeniu pilot pokazuje niebieskie logo Home Assistant,
napis **Smart Remote**, poniżej **KubAI**, oraz pasek postępu.
Domyślnie ekran jest widoczny przez **2 sekundy**. Wi-Fi łączy się w tle.

## Pliki do wgrania i kopiowania

- `smart-remote.yaml` — pełna konfiguracja pilota dla ESPHome 2026.10.2 / ESP32-S3 N16R8.
- `txt/smart-remote-v9.txt` — identyczna kopia całego kodu jako TXT.
- `Instrukcja-Smart-Remote-V9.pdf` — opis ustawień i podgląd.
- `podglad-start-v9.png` — ekran powitalny i ustawienie czasu.

Na GitHubie otwórz plik TXT i wybierz **Raw**, a następnie **Ctrl+A → Ctrl+C**.
Możesz też pobrać całe repozytorium przez **Code → Download ZIP** i użyć folderu `v9`.
Zastąp całą konfigurację `smart-remote.yaml` w ESPHome, zweryfikuj i zainstaluj.
Pozostaw własne `wifi_ssid` i `wifi_password` w `secrets.yaml`.

Wystarczy aktualizacja pilota. Pakiety HA z działającej wersji V8 pozostają aktualne.
Informacje o termostatach i pokojach są w [instrukcji V8](../v8/README.md).

## Zmiana czasu na pilocie

Wejdź w **Ustawienia → Konfiguracja → Czasy → Ekran startowy**.
Enkoder zmienia czas co 1 sekundę, a **OK zapisuje** wybraną wartość.

- **2 s** — ustawienie domyślne.
- **0 s** — wyłącza ekran powitalny.
- Zakres: **0–10 s**. Wartość jest zachowywana po uśpieniu i zaniku zasilania.

Dowolny nowy przycisk lub ruch enkodera pozwala pominąć ekran. Zdarzenie,
które go zamyka, zostaje zużyte i nie otwiera od razu pozycji menu.
Przycisk OK przytrzymywany podczas wybudzenia trzeba najpierw puścić;
nie uruchamia sam mikrofonu ani potwierdzenia.

Czas bezczynności jest liczony od zamknięcia ekranu powitalnego.
Po pokazaniu godziny zostaje zachowane co najmniej 10 s jej widoczności.
Gdy urządzenie jest offline, ekran również kończy się po zadanym czasie.
Menu, lampy, gniazda, rolety, termostaty, IR i test mikrofonu działają jak w V8.
Domyślne czasy pozostają: mikrofon **2 s**, deep sleep **25 s**, powrót do menu **10 s**.

Logo jest osadzone jako glif z tej samej czcionki ikon co reszta interfejsu.
Wgrywasz jeden plik YAML; podczas wybudzania pilot nie pobiera żadnej grafiki z sieci.

## Weryfikacja

Sprawdzono nieblokujące odmierzanie czasu, wyłączenie ekranu, granice ustawienia,
wybudzanie przytrzymanym OK, pomijanie ekranu i zachowanie istniejących funkcji.
Układ sprawdzono rzeczywistymi metrykami czcionek dla 240×320. Końcowy YAML
przechodzi walidację i kompilację ESP32-S3. Podgląd używa przykładowych wartości.
