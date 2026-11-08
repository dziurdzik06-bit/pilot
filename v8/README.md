# Smart Remote — V8: pokoje i termostaty z HA

Pełna konfiguracja ESPHome 2026.10.2 dla ESP32-S3 N16R8. Zachowuje lampy,
rolety, gniazda, IR, czujniki i lokalny test mikrofonu z V7. Dodaje sterowanie
termostatami Home Assistant 2026.11.0 i szybkie łączenie z Wi-Fi.

## Pobieranie

- **Code → Download ZIP** na stronie repozytorium pobiera całość. Korzystaj z katalogu `v8`.
- Pliki w `txt` zawierają pełne konfiguracje; otwórz **Raw**, następnie **Ctrl+A → Ctrl+C**.
- `smart-remote.yaml` / `txt/smart-remote-v8.txt`: pełny kod pilota.
- `home-assistant/pilot_climates.yaml` / `txt/pilot_climates.txt`: nowy pakiet HA.
- Instrukcja PDF i podgląd ekranów opisują przygotowanie termostatów.

## Instalacja

1. Zastąp konfigurację pilota całą zawartością `smart-remote.yaml`, zweryfikuj
   i zainstaluj w ESPHome. Pozostaw własne `wifi_ssid` i `wifi_password` w `secrets.yaml`.
2. Zapisz nowy pakiet jako **`/config/packages/pilot_climates.yaml`**.
   Zachowaj działające pakiety lamp, gniazd i rolet oraz obecną sekcję
   `homeassistant: packages:`. Pakiet termostatów jest dodatkiem.
3. Sprawdź konfigurację HA i przeładuj skrypty. Jeśli skrypt się nie pojawi,
   uruchom HA ponownie. Oczekiwany identyfikator: **`script.pilot_climates`**.
4. Utwórz etykietę **Termostaty pilota** i przypisz ją bezpośrednio do istniejącej
   encji `climate.*` termostatu Salon, np. `climate.salon`. Kolejne termostaty
   otrzymują tę samą etykietę. W integracji ESPHome pozostaw włączone wykonywanie akcji HA.

Istniejący `generic_thermostat` w HA pozostaje źródłem sterowania ogrzewaniem.
Nie zmieniaj jego przekaźnika, czujnika temperatury, tolerancji ani minimalnego
czasu przełączeń z powodu tej aktualizacji. Nowy pakiet dodaje wyłącznie skrypt
obsługujący pilota; nie tworzy dodatkowych termostatów ani przekaźników.

## Pokoje definiujesz w HA

Na start pakiet HA udostępnia kolejno: **Salon, Pokój babci, Pokój balkon,
Sypialnia rodzice, Sypialnia Niko, Łazienka dół, Łazienka góra, Garderoba,
Spiżarka i Korytarz**. Te nazwy są zapisane wyłącznie w `configured_rooms`
w pakiecie HA. W firmware nie ma listy pokojów.

Pokoje bez termostatu mają kafelek **Brak termostatu**. Nie sterują innym pokojem
i nie pokazują wymyślonych temperatur. Na początku aktywny może być tylko Salon.

Docelowo korzystaj z obszarów HA:

1. Utwórz lub wybierz obszary odpowiadające pokojom w ustawieniach HA.
2. Nadaj wybranym **obszarom** etykietę **Pokoje pilota**.
3. Przypisz każdy termostat do właściwego obszaru i nadaj jego **encji** etykietę
   **Termostaty pilota**.

Gdy istnieje co najmniej jeden oznaczony obszar, lista na pilocie pochodzi już
wyłącznie z oznaczonych obszarów i zastępuje listę startową. Oznacz wszystkie
pokoje, które chcesz widzieć. Dodawanie obszarów, zmiana nazw i etykiet nie
wymagają flashowania ani przeładowywania skryptów.

Przy liście startowej termostat jest dopasowywany po nazwie obszaru, a jeśli
nie ma obszaru — po swojej nazwie w HA. Dlatego istniejący termostat o nazwie
Salon może działać od razu po dodaniu etykiety.

Jeżeli w pokoju są dwa oznaczone termostaty, pilot pokaże konieczność wyboru.
W pakiecie HA możesz jawnie wskazać właściwy termostat, np.:

```yaml
thermostat_overrides:
  room.salon: climate.salon
```

Ten przykład dotyczy listy startowej. Dla obszaru HA użyj `area.<identyfikator_obszaru>`,
np. `area.salon`. Encja nadal musi mieć etykietę **Termostaty pilota**.
Identyfikatory encji i obszarów zastąp swoimi. Po edycji samego pakietu przeładuj skrypty HA.

## Obsługa

- **Lista pokojów:** GÓRA/DÓŁ wybiera kafelek, OK otwiera kartę, LEWO wraca.
  Enkoder przełącza strony; przy jednej stronie wybiera kafelek. Są cztery pokoje na stronę.
- **Karta termostatu:** GÓRA/DÓŁ wybiera temperaturę, tryb lub profil.
- **Temperatura:** enkoder zmienia ją o krok podany przez termostat HA.
  Dla Twojego `precision: 0.5` jest to **0,5°C**. Zmiana trafia do HA po
  350 ms przerwy w obracaniu; OK może wysłać ją od razu.
- **Tryb i profil:** enkoder wybiera, **OK zatwierdza**. Samo przewijanie nie
  włącza ogrzewania. Dla typowego generic_thermostat są to Wyłączony/Ogrzewanie
  oraz skonfigurowane profile, np. Poza domem, Komfort, Dom i Noc.
- LEWO wraca do listy i odrzuca zmiany, które nie zostały wysłane.

Zakres temperatury, krok, jednostka, tryby i profile pochodzą z HA. Brakujące
odczyty pokazują `--`; nieaktualne dane lub utrata HA blokują sterowanie.
Stan grzania i temperatura aktualna pochodzą z konkretnego termostatu HA,
a nie z lokalnego DS18B20 pilota. Ten ostatni nadal jest na pasku głównym.

## Wi-Fi i stały adres z AP

W V8 aktywne są:

```yaml
wifi:
  # Istniejące SSID/hasło pozostają w secrets.yaml.
  fast_connect: true
  power_save_mode: none
  reboot_timeout: 0s
api:
  reboot_timeout: 0s
```

To fragment opisujący ustawienia, a nie zamiennik pełnego pliku.
**Zarezerwuj w AP/routerze wybrany adres dla MAC pilota.** Adres nadaje
DHCP. ESPHome/OTA domyślnie korzysta z nazwy `smart-remote.local`. `fast_connect`
wykorzystuje zapamiętany punkt dostępowy i kanał, a przy nieudanej próbie wraca
do szukania sieci. Nie pomija uwierzytelniania ani uzyskania adresu DHCP.

Domyślne czasy pozostają: mikrofon **2 s**, deep sleep **25 s**, powrót do menu
**10 s**. Ustawienia zapisane wcześniej na pilocie mają pierwszeństwo.

## Weryfikacja

Pakiet sprawdzono testami rzeczywistych szablonów Jinja i lambd C++, walidacją
ESPHome, kompilacją dla ESP32-S3 oraz kontrolą układu ekranu 240×320.
Podgląd używa danych przykładowych. Sterowanie wymaga sprawdzenia z docelową
instancją HA i fizycznymi termostatami.
