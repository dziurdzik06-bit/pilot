# Smart Remote — V11: wersja SW w statystykach

W **Ustawienia → Statystyki → System** pojawia się osobny wiersz **SW: V11**.
Pokazuje wersję oprogramowania pilota. Ta sama wartość jest zapisana w metadanych
projektu ESPHome; definiuje ją jeden wpis `substitutions.sw_version`.

Wgraj pełny `smart-remote.yaml` albo identyczną zawartość `txt/smart-remote-v11.txt`.
Na GitHubie otwórz TXT przez **Raw**, następnie użyj **Ctrl+A → Ctrl+C**.
Wystarczy aktualizacja pilota. Własne dane Wi-Fi pozostają w `secrets.yaml`.

Po wgraniu sprawdź w statystykach **SW: V11**. Pozostała zawartość ekranu System
jest zachowana: rozmiar kodu, CPU, czas pracy, źródło zegara, sieć, RAM i PSRAM.
Zachowana jest również poprawka V10 uruchamiająca podświetlenie po pierwszej
pełnej klatce oraz wszystkie funkcje sterowania i zapisane ustawienia.
