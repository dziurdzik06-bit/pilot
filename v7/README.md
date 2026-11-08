# Smart Remote — V7

Gotowy pakiet dla ESP32-S3 N16R8, ESPHome 2026.10.2 i Home Assistant 2026.11.0.

## Pobieranie i kopiowanie

- **Całość:** na stronie repozytorium wybierz **Code → Download ZIP** i rozpakuj archiwum.
- **Kopiowanie tekstu:** otwórz plik z katalogu `txt`, wybierz **Raw**, następnie **Ctrl+A → Ctrl+C**.
- Kopie TXT zawierają pełny kod identyczny z odpowiednimi plikami YAML.

## Pliki

| Plik | Przeznaczenie |
| --- | --- |
| `smart-remote.yaml` | Pełna konfiguracja pilota; zapisz w ESPHome jako `smart-remote.yaml`. |
| `home-assistant/pilot_sockets.yaml` | Pakiet gniazd do `/config/packages/pilot_sockets.yaml`. |
| `home-assistant/pilot_covers.yaml` | Pakiet rolet do `/config/packages/pilot_covers.yaml`. |
| `txt/smart-remote-v7.txt` | Cała konfiguracja pilota w formie TXT. |
| `txt/pilot_sockets.txt` | Pakiet gniazd w formie TXT. |
| `txt/pilot_covers.txt` | Pakiet rolet w formie TXT. |
| `Instrukcja-Smart-Remote-V7.pdf` | Instrukcja z opisem i podglądem ekranów. |

## Instalacja

1. Wklej całą zawartość konfiguracji pilota do ESPHome, zweryfikuj i zainstaluj.
   Wi-Fi korzysta z Twoich istniejących `!secret wifi_ssid` oraz `!secret wifi_password`.
2. Do istniejącego `/config/packages/` w HA dołóż dwa nowe pakiety z katalogu `home-assistant`.
   Zachowaj działający `pilot_lights.yaml` i obecną sekcję `homeassistant: packages:` — te pliki są dodatkiem do działającej konfiguracji lamp.
3. Sprawdź konfigurację HA i przeładuj skrypty; jeżeli nowe skrypty się nie pojawią, uruchom HA ponownie.
4. Przypisz etykietę **Gniazda pilota** bezpośrednio encjom `switch.*`, a **Rolety pilota** encjom `cover.*`.
   Nowe urządzenia dobierasz już w HA, bez kolejnego flashowania pilota.
5. W integracji ESPHome pilota pozostaw włączone wykonywanie akcji HA.

Gniazda mają ON/OFF oraz opcjonalne pomiary mocy, prądu, napięcia i energii.
Pomiary dobierają się po urządzeniu i `device_class`; niejednoznaczne powiązania można ustawić w `measurement_overrides` w pakiecie gniazd.
Rolety pokazują funkcje udostępnione przez encję HA. Oddzielna bramka Elero RF nie jest częścią tego firmware.

V7 zawiera też większe regulatory lamp, przyspieszanie enkodera, przewijane menu,
stałą dolną podpowiedź i przyciski Sterowania jeden pod drugim.
Domyślne czasy: mikrofon **2 s**, deep sleep **25 s**, powrót do menu **10 s**.
Wartości zapisane wcześniej w pamięci urządzenia mają pierwszeństwo nad domyślnymi.

Walidacja, kompilacja i testy lokalne zakończyły się poprawnie. Nowe gniazda i rolety
wymagają sprawdzenia na docelowej instalacji.
