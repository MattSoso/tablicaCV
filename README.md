# Tablica CV — Mateusz Sosiński

Strona portfolio w jednym pliku `index.html`, ze zdjęciami, CV i animacjami osadzonymi w HTML.

## Automatyczna aktualizacja mateuszsosinski.pl

Workflow `.github/workflows/deploy.yml` wysyła `index.html` na hosting cal.pl przez SSH/SFTP na porcie 22:
- po merge'u lub pushu do `main`, gdy zmieni się strona lub workflow;
- ręcznie: **Actions → Deploy mateuszsosinski.pl → Run workflow** (gałąź `main`).

Bez danych dostępowych workflow pomija wdrożenie i zapisuje informację w podsumowaniu. Dodanie sekretów samo nie uruchamia wysyłki — po konfiguracji uruchom workflow ręcznie.

### 1. Sprawdź hosting cal.pl

Włącz SSH w panelu hostingu. Odczytaj rzeczywisty adres serwera, login i pełny katalog dokumentów domeny **mateuszsosinski.pl**. Nie zgaduj katalogu na podstawie nazwy domeny; potwierdź go w panelu lub przez `pwd` po wejściu do katalogu strony.

Instrukcja cal.pl: https://www.cal.pl/pomoc/logowanie-do-ssh/

### 2. Dodaj pięć sekretów w GitHubie

W repozytorium: **Settings → Secrets and variables → Actions → New repository secret**.

| Nazwa | Wartość |
| --- | --- |
| `SSH_HOST` | Adres serwera SSH z cal.pl, bez `https://`. |
| `SSH_USER` | Login konta z działającym dostępem SSH. |
| `SSH_PASSWORD` | Hasło tego konta. |
| `SSH_KNOWN_HOSTS` | Zweryfikowany wpis klucza serwera z `known_hosts` — instrukcja poniżej. |
| `DEPLOY_PATH` | Pełna ścieżka do istniejącego katalogu strony mateuszsosinski.pl. |

Hasła wpisuj wyłącznie jako sekret GitHub Actions, nigdy do pliku, commita ani issue.

### 3. Klucz serwera SSH

Na swoim komputerze połącz się z dokładnie tym adresem, który wpiszesz jako `SSH_HOST`:

```sh
ssh LOGIN@ADRES_SERWERA
```

Przy pierwszym połączeniu porównaj odcisk klucza z informacją od cal.pl, zanim go zaakceptujesz. Po udanym logowaniu zakończ sesję poleceniem `exit`. Następnie na swoim komputerze:

```sh
ssh-keygen -F ADRES_SERWERA -f ~/.ssh/known_hosts
```

Skopiuj linię lub linie kluczy (bez komentarzy zaczynających się od `#`) do sekretu `SSH_KNOWN_HOSTS`. Wpis musi dotyczyć tego samego adresu co `SSH_HOST`. Wdrożenie sprawdza klucz serwera i nie akceptuje automatycznie nowych ani zmienionych kluczy.

### 4. Pierwsze wdrożenie

Uruchom **Actions → Deploy mateuszsosinski.pl → Run workflow**. Zielony wynik z krokiem **Upload and activate index.html** oznacza, że przesłano plik i porównano jego SHA-256 na serwerze.

Otwórz https://mateuszsosinski.pl i sprawdź stronę. Domena musi wskazywać na ten hosting, a jej katalog startowy musi odpowiadać `DEPLOY_PATH`. Stary plik `index.php` lub konfiguracja hostingu mogą mieć pierwszeństwo przed `index.html`.

Wysyłany jest tylko `index.html`. Pozostałe pliki na hostingu są zachowywane. Plik najpierw trafia pod tymczasową nazwę, a po sprawdzeniu zastępuje docelowy `index.html`. Jeśli w przyszłości dodasz osobne zdjęcia, CSS lub JS, rozbuduj listę wdrażanych plików.
