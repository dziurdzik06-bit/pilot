# Smart Remote — V13

Lista pokojów i karta termostatu pokazują teraz ten sam **stan pracy** z Home Assistant:

- **Grzanie** — termostat aktualnie ogrzewa pomieszczenie.
- **Przerwa** — regulacja jest włączona, ale termostat chwilowo nie grzeje.
- **Wyłączony** — termostat jest wyłączony.

Wiersz **Tryb: Ogrzewanie** nadal oznacza wybrany tryb regulacji. Może więc
występować jednocześnie ze stanem **Przerwa**. Jeśli Home Assistant nie podaje
stanu pracy, pilot pokazuje **Brak stanu pracy**, zamiast zgadywać, czy grzeje.

Temperatura aktualna i zadana zachowują dotychczasowe znaczenie. Sterowanie
termostatem, czasy, Wi-Fi i pozostałe funkcje V12 pozostają bez zmian.

## Wgranie

- [Pełny YAML](smart-remote.yaml)
- [Pełny tekst do kopiowania](txt/smart-remote-v13.txt) — kliknij **Raw**, potem **Ctrl+A → Ctrl+C**.

Zastąp całą konfigurację pilota i skompiluj ją z własnym `secrets.yaml`.
Wystarczy aktualizacja pilota; **pakietów Home Assistant nie trzeba zmieniać**.
Po wgraniu w **Statystyki → System** zobaczysz **SW: V13**.

Sprawdzono zakres zmian, dopasowanie opisów do istniejących pól oraz kompilację
dla ESP32-S3. Nie wykonano testu na fizycznym urządzeniu.
