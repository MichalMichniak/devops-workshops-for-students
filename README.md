# Warsztaty DevOps dla studentów

## Wymagania sprzętowe

### Windows

* Windows 11 64-bit: Home lub Pro wersja 21H2 lub wyższa albo wersja Enterprise/Education 21H2 i wyższa
* Windows 10 64-bit: Home lub Pro 21H1 (build 19042) lub wyższa albo Enterprise/Education 20H2 (build 19042) i wyższa
* 64-bit processor with Second Level Address Translation (https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
* 8GB system RAM lub więcej
* BIOS-level hardware wsparcie dla wirtualizacji włączone (więcej info: https://docs.docker.com/desktop/troubleshoot/tdevops-workshops-for-studentsopics/#virtualization)

### Linux

Aby zainstalować Dockera musimy posiadać 64bitową wersję Ubuntu, jedną z wybranych:
* Ubuntu Jammy 22.04 (LTS)
* Ubuntu Impish 21.10
* Ubuntu Focal 20.04 (LTS)
* Ubuntu Bionic 18.04 (LTS)

### MacOS

* MacOS w wersji 10.15 lub wyższej. To jest Catalina, Big Sur, Monterey. Zalecane jest zaktualizowanie systemu do najnowszej wersji
* 4GB system RAM lub więcej
* VirtualBox przed wersją 4.3.30 nie może być zainstalowany, ponieważ nie jest kompatybilny z Docker Desktop

## Wymagane oprogramowanie

* Git (https://git-scm.com/)
* Python3 (https://www.python.org/)
* Docker (https://www.docker.com/)
* Minikube with kubectl (https://minikube.sigs.k8s.io/docs/start/)
* Pobranie i zainstalowanie Linux kernel update package (https://learn.microsoft.com/en-us/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package) (Windows)

## Wymagane konta internetowe

* GitHub (https://github.com/)
* DockerHub (https://hub.docker.com/)
* Snyk (https://snyk.io/)

## Plan warsztatu

1. [Instalacja narzędzi](#1-Instalacja-narzędzi)
2. [Założenie kont na serwisach: Github, Docker Hub, Snyk](#2-Założenie-kont-na-serwisach-Github-Docker-Hub-Snyk)
3. [Stworzenie katalogu roboczego i sklonowanie repozytorium](#3-Stworzenie-katalogu-roboczego-i-sklonowanie-repozytorium)
4. [Przygotowanie wirtualnego środowiska Python](#4-Przygotowanie-wirtualnego-środowiska-Python)
5. [Uruchomienie aplikacji, bazy danych i testów jednostkowych](#5-Uruchomienie-aplikacji-bazy-danych-i-testów-jednostkowych)
6. [Konteneryzacja aplikacji](#6-Konteneryzacja-aplikacji)
7. [Orkiestryzacja aplikacji z użyciem narzędzia docker-compose](#7-Orkiestryzacja-aplikacji-z-użyciem-narzędzia-docker-compose)
8. [Ciągła integracja i wydanie](#8-Ciągła-integracja-i-wydanie)
9. [Wdrożenie aplikacji na platformie Kubernetes](#9-Wdrożenie-aplikacji-na-platformie-Kubernetes)
10. [Ćwiczenia](#10-Ćwiczenia)

---

## 1. Instalacja narzędzi

### 1.1. Git

Pełna dokumentacja: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git

#### Windows

Przechodzimy na stronę z instalkami Gita dla Windowsa: https://git-scm.com/download/win. Następnie pobieramy odpowiednią wersję i instalujemy.

#### Linux

Dla dystrubucji opartych na Debianie wykonujemy polecenie:

```sh
sudo apt install git-all
```

#### MacOS

Wykonujemy polecenie, po którego wykonaniu pokaże nam się propozycja instalacji:

```sh
git --version
```

---

### 1.2. Python

#### Windows

Pobieramy plik instalacyjny Pythona w odpowiedniej wersji (najlepiej ostatniej stabilnej) ze strony: https://www.python.org/downloads/ i instalujemy.

#### Linux

Wykonujemy poniższe polecenia, które zaktualizują narzędzie apt-get, zainstalują Pythona w najnowszej dostępnej wersji na obecnego narzędzi pakietów i zaktualizują wersję menadżera pakietów (pip).

```sh
sudo apt-get update && \
sudo apt-get install python3 python3-venv python3-pip
```

#### MacOS

Pobieramy plik instalacyjny Pythona w odpowiedniej wersji (najlepiej ostatniej stabilnej) ze strony: https://www.python.org/downloads/macos/ i instalujemy.

---

### 1.3. Docker

#### Windows

Pełna dokumentacja: https://docs.docker.com/desktop/install/windows-install/

1. Przed zainstalowaniem WSL należy uruchomić funkcjonalność "Virtual Machine Platform" poprzez wykonanie polecenia w PowerShellu uruchomionym jako administrator (https://learn.microsoft.com/en-us/windows/wsl/install-manual#step-3---enable-virtual-machine-feature):

    ```sh
    dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
    ```

2. Instalujemy WSL (Windows Subsystem for Linux)

    ```sh
    wsl --install
    ```

    Ta komenda zainstaluje potrzebne rzeczy do uruchomienia WSL i zainstaluje Linuxa w domyślnej dystrybucji Ubuntu.

3. Restartujemy Windows

4. Tworzymy Linuxowego użytkownika. Można to zrobić poprzez otworzenie dystrybucji Linuxa posługując się np. Start menu. Zostaniemy poproszeni o podanie nazwy  użytkownika i hasła do nowo tworzonego użytkownika. Użytkownik ten zostanie powiązany z konkretną dystrybucją.

5. Podnosimy wersję WSL z wersji 1 do 2. Najpierw możemy sprawdzić, która obecnie wersja WSL jest używana wykonując polecenie:

    ```sh
    wsl -l -v
    ```

    Aby wybrać domyślną wersję WSL jako wersję 2, należy wykonać poniższą komendę:

    ```sh
    wsl --set-default-version 2
    ```
    
    > Więcej informacji: https://docs.microsoft.com/en-us/windows/wsl/install-win10

6. Pobieramy aplikację Docker Desktop: https://docs.docker.com/desktop/install/windows-install/

7. Instalujemy Docker Desktop.

    * Zainstaluj *Linux Kernel update package*: https://docs.microsoft.com/windows/wsl/wsl2-kernel
    * Uruchom instalator Docker Desktop. Po pojawieniu się okienka instalacji należy się upewnić, że wybrana jest opcja użycia *WSL 2* zamiast *Hyper-V*.
    * Należy także sprawdzić czy włączona jest integracja z wsl oraz z używaną dystrybucją Linuxa. W tym celu należy wejść w ustawienia Docker Desktop, otworzyć zakładkę *Resources* i włączyć wybrane integracje.

#### Linux

- W większości przypadków wystarczy wykonać komendy:

    ```sh
    sudo apt-get update &&\
    sudo apt-get install docker.io docker-compose
    ```

- Można także posłużyć się oficjalną dokumentacją (za https://docs.docker.com/engine/install/ubuntu):

  - Odinstalowujemy stare wersje Dockera:

     ```sh
     sudo apt-get remove docker docker-engine docker.io containerd runc
     ```

   - Instalujemy Dockera przy pomocy repozytorium:

      - Aktualizujemy pakiet apt i instalujemy wybrane pakiety:

        ```sh
        sudo apt-get update && \
        sudo apt-get install \
        ca-certificates \
        curl \
        gnupg \
        lsb-release
        ```

      - Dodajemy oficjalny klucz dockerowy GPG:

        ```sh
        sudo mkdir -p /etc/apt/keyrings
        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
        ```

      - Konfigurujemy repozytorium:

        ```sh
        echo \
        "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
        $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
        ```

#### MacOS

Pełna dokumentacja: https://docs.docker.com/desktop/install/mac-install/

1. Pobieramy instalator w zależności od rodzaju procesora (z pełnej dokumentacji powyżej).

2. Instalujemy przy pomocy graficznego interfejsu uruchamiając instalator, lub przy pomocy wiersza poleceń (szczegóły w pełnej dokumentacji).

---

### Kroki post-instalacyjne

**Kroki należy wykonać dla wszystkich systemów.**

Zmiana pozwoli uruchamiać polecenia dockera z poziomu użytkownika (a nie przez polecenie `sudo`).

#### 1. Dodajemy użytkownika do grupy docker.

Jeśli wcześniej instalator tego nie zrobił, tworzymy grupę `docker`:

```sh
sudo groupadd docker
```

i dodajemy do niej swojego użytkownika:

```sh
sudo usermod -aG docker $USER
```

> By sprawdzić czy nasz użytkownik jest w grupie `docker` jako użytkownik należy wykonać polecenie `groups`.

Wylogowujemy się i logujemy ponownie.

#### 2. Weryfikacja poprawności instalacji.

Wykonujemy polecenie:

```sh
docker ps
```

Powinna się nam wyświetlić lista uruchomionych kontenerów (pusta):

```text
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

Można także uruchomić testową aplikację:

```sh
docker run hello-world
```

#### Zobacz także: https://docs.docker.com/engine/install/linux-postinstall/

---

## 2. Założenie kont na serwisach: Github, Docker Hub, Snyk

* Konto GitHub będzie nam potrzebne w celu stworzenia pipelinu (CI) wykorzystując narzędzie GitHub Actions. Pipeline służy do automatyzacji pewnych procesów. W tym wypadku tymi procesami będą: testy jednostkowe, lintowanie kodu, budowanie kontenera aplikacji i wysyłanie go na repozytorium, statyczna analiza kodu. (https://github.com/)
* Konto na repozytorium Dockerowym (Docker Hub) będzie wykorzystane w celu przechowania na nim naszego zbudowanego kontenera aplikacji. (https://hub.docker.com/)
* Konto Snyk zostanie wykorzystane w celu wykonania statycznej analizy kodu. (https://snyk.io/)

---

## 3. Stworzenie katalogu roboczego i sklonowanie repozytorium

Forkujemy repozytorium poprzez otworzenie oficjalnego repozytorium ```https://github.com/Jeremiaszmacura/devops-workshops-for-students``` i kliknięcie ikony z napisem fork (prawy góry róg). 
Po udanym forku klonujemy nowo utworzone repozytorium do wybranego katalogu roboczego na naszym systemie przy pomocy polecenia ```git clone https://github.com/Jeremiaszmacura/devops-workshops-for-students``` wykonanego z poziomu `cmd/powershell/git` lub `bash/bash/sh`.

---

## 4. Przygotowanie wirtualnego środowiska Python

Tworzymy wirtualne środowisko przy pomocy modułu `venv`: 

```sh
python3 -m venv .venv
```

A następnie aktywujemy je:

### Linux/MacOS

```sh
source .venv/bin/activate
```

### Windows

```sh
.venv\Scripts\activate
```

---

Dzięki temu stworzymy odseparowane środowisko do pracy nad aplikacją, a wszystkie zainstalowane biblioteki/zależności pozostaną jedynie w tym środowisku i nie będą miały wpływu na pozostałe projekty Pythonowe znajdujące się w naszym systemie.

> W przypadku problemów z aktywowaniem środowiska wirtualnego w systemie Windows można skorzystać z komendy Powershell:
> 
> ```sh
> Set-ExecutionPolicy Unrestricted -Scope Process
> ```

---

## 5. Uruchomienie aplikacji, bazy danych i testów jednostkowych

### 5.1. Konfiguracja środowiska i instalacja zależności

Instalujemy naszą aplikację jako wykorzystując setuptools. 
Poniższe polecenie wykonujemy w katalogu głównym projektu:

```sh
python -m pip install -e ".[dev]"
```

Budujemy paczkę (przy zmianach w projekcie każdorazowo przed wybudowaniem obrazu dokerowego).

```sh
python setup.py bdist_wheel
```

Przed uruchomieniem aplikacji musimy zadbać o bazę danych, z którą aplikacja będzie się próbowała połączyć. 
Wystarczy, że skorzystamy z Dockera i wykonamy poniższe polecenie, które zaciągnie obraz bazy danych PostgreSQL w wersji 14 z oficjalnego repozytorium, a następnie na podstawie tego obrazu zostanie uruchomiony kontener z określonymi zmiennymi środowiskowymi, widocznymi wewnątrz niego.

---

### 5.2. Uruchomienie kontenera bazy danych i aplikacji

1. Uruchomienie bazy danych

```sh
docker run --name postgres_workshops -e POSTGRES_DB=dev_database -e POSTGRES_USER=dev_user -e POSTGRES_PASSWORD=dev_user -p 5432:5432 -d postgres:14
```

2. Uruchomienie aplikacji w trybie developerskim:

```sh
flask --app flaskr.app run
```

Aplikacja powinna być dostępna pod adresem http://localhost:5000 

---

### 5.3. Uruchomienie testów jednostkowych

Sprawdzamy czy testy jednostkowe przechodzą:

```sh
python setup.py test
```

---

## 6. Konteneryzacja aplikacji

### Dockerfile

Przygotowany plik `Dockerfile` pozwoli nam na zbudowanie obrazu Dockera, zawierającego naszą aplikację i wszystkie niezbędne do jej uruchomienia zależności.
Na podstawie wybudowanego obrazu będzie możliwe uruchomienie kontenera.

```dockerfile
FROM python:3.10
WORKDIR /app
COPY ./dist/flaskr-0.1.0-py3-none-any.whl .
RUN pip3 install flaskr-0.1.0-py3-none-any.whl
EXPOSE 5000
CMD ["gunicorn","-b","0.0.0.0:5000","-w","1","flaskr.app:create_app()"]
```

- ```FROM python:3.10``` określa bazowy obraz, który będziemy rozbudowywać. Jest to obraz z zainstalowanym Pythonem w wersji 3.10. 
- ```WORKDIR /app``` powoduje, że wszystkie polecenia zostaną domyślnie wykonane pod tą ścieżką w obrazie. 
- ```ENV FLASK_APP=flaskr/app.py``` oraz ```ENV FLASK_RUN_HOST=0.0.0.0``` ustawiają zmienne środowiskowe widoczne w obrazie.
- ```COPY ./dist/flaskr-0.1.0-py3-none-any.whl .``` powoduje przekopiowanie wybranych plików pomiędzy naszą maszyną hostującą, a obrazem, który zostanie stworzony. 
- ```RUN pip3 install flaskr-0.1.0-py3-none-any.whl``` wykonuje polecenie instalacji paczki z aplikacją.
- ```EXPOSE 5000``` pozwala na udostępnienie portu 5000 obrazu na zewnątrz, dzięki czemu możemy wykonywać na ten port zapytania z naszej maszyny hostującej i komunikować się z aplikacją w kontenerze. 
- ```CMD ["gunicorn","-b","0.0.0.0:5000","-w","1","flaskr.app:create_app()"]``` to polecenie zostanie wykonane za każdym razem gdy kontener jest uruchamiany.

---

### Wybudowanie i uruchomienie aplikacji przy pomocy Dockerfile

W momencie kiedy Dockerfile jest już gotowy możemy na jego podstawie utworzyć obraz:

```sh
docker build -t flask-app:develop .
```

Parameter ```-t``` oznacza nazwę pod jaką zostanie utworzony obraz.

Ponieważ chcemy by kontener aplikacji i bazy danych komunikowały się między sobą tworzymy sieć typu bridge:

```sh
docker network create -d bridge my-bridge-network
```

Zatrzymujemy i usuwamy poprzednio utworzony kontener bazy danych i uruchamiamy go jeszcze raz, tym razem z nowo utworzoną siecią:

```sh
docker container stop postgres_workshops
docker container rm postgres_workshops
docker run --name postgres_workshops -e POSTGRES_DB=dev_database -e POSTGRES_USER=dev_user -e POSTGRES_PASSWORD=dev_user --network=my-bridge-network -d postgres:14
```

Teraz, na podstawie utworzonego obrazu tworzymy i uruchamiamy kontener z aplikacją:

```sh
docker run -d -e FLASK_DEBUG="True" -e DATABASE_URI="postgresql://dev_user:dev_user@postgres_workshops:5432/dev_database" -p 5000:5000 --network=my-bridge-network --name flask_app flask-app:develop
```

- ```-d``` oznacza tryb ```detach``` podczas, którego kontener pracuje w tle, a na konsolę jest jedynie wypisywane ID tego kontenera. 
- ```-e``` powoduje dodanie zmiennej środowiskowej do uruchamianego kontenera. 
- ```-p 5000:5000``` mapuje lokalny port kontenera (5000) na port hosta (5000).
- ```--network="my-bridge-network"``` odpowiednio ustawia utworzoną wcześniej sieć.
- ```--name flask_app``` nadaje nawzę kontenerowi. 

Na końcu podajemy nazwę obrazu, na podstawie, którego ma zostać stworzony kontener.

> **Warto zwrócić uwagę, że w URI do bazy danych podany jest tym razem `postgres_workshops`.**

---

### Przydatne komendy

Wypisanie logów z kontenera na standardowe wyjście:

```sh
docker logs <container>
```

Zatrzymnie działania uruchomionego kontenera:

```sh
docker stop <container>
```

Uruchomienie zbudowanego kontenera:

```sh
docker start <container>
```

Usunięcie zatrzymanego kontenera:

```sh
docker rm <container>
```

Usunięcie obrazu:

```sh
docker rmi <image>
```

Wypisanie dostepnych sieci dockerowych:

```sh
docker network ls
```

Usunięcie sieci dockerowej:

```sh
docker network rm <network>
```

Usunięcie wszystkich kontenerów i obrazów:

```sh
docker system prune
```

---

### .dockerignore

Ten plik działa podobnie jak plik .gitignore w przypadku git'a. Pozwala określić katalogi i pliki, które nie będą kopiowane w przypadku komendy COPY w Dockerfile (choćby zawierały się w ścieżce do skopiowania w Dockerfile).

---

## 7. Orkiestryzacja aplikacji z użyciem narzędzia docker-compose

Cały stos aplikacji może być uruchomiony za pomocą polecenia *docker-compose* Dockera. 
Compose jest narzędziem do definiowania i uruchamiania aplikacji Dockera składających się z wielu kontenerów/aplikacji.

W tym celu przygotowany został plik `docker-compose.yaml`, w którym zdefiniowane jest sama usługi aplikacji, baza danych i odpowiednie dane konfiguracyjne.

> **Uwaga:** Polecenie powinno być wykonane w folderze, w którym znajduje się odpowiedni plik `docker-compose.yaml`. W przeciwnym razie należy go wskazać za pomocą opcji `-f`.

**Zanim skorzystamy z narzędzia jakim jest docker-compose musimy zakończyć działanie i żywot naszych obecnie działających kontenerów przy pomocy poleceń:**

```sh
docker stop flask_app postgres_workshops
docker rm flask_app postgres_workshops
```

---

### docker-compose.yaml

Poniższy plik docker-compose.yaml definiuje zarówno kontenery z ich specyfikacją, które mają zostać zbudowane jak i specyfikację sieci, w której mają pracować. Pierwsza z aplikacji określona w tym pliku to nasza aplikacja, napisana w frameworku *Flask*:


```yaml
version: '3.8'

services:

  flask-app:
    image: flask-app
    build:
      context: .
      dockerfile: ./Dockerfile
    ports:
      - 5000:5000
    environment:
      FLASK_DEBUG: 'True'
      DATABASE_URI: postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@${POSTGRES_HOST}:5432/${POSTGRES_DB}
    restart: on-failure
    depends_on:
      - database

  database:
    image: postgres:14
    env_file: .env
    environment:
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_DB=${POSTGRES_DB}
    restart: always
```

* ```image``` określa nazwę obrazu, który ma zostać użyty do zbudowania kontenera.
* ```build``` zawiera dodatkowe parametry wykorzystane w procesie budowania kontenera na bazie wybranego obrazu. ```context``` określa kontekst, a ```dockerfile``` zawiera ścieżkę do pliku Dockerfile, na podstawie, którego zostanie zbudowany obraz.
* ```ports``` mapuje porty pomiędzy maszyną hostującą, a kontenerem ```HOST:CONTAINER```.
* ```environment``` pozwala na dodanie zmiennych środowiskowych do tworzonego kontenera.
* ```restart``` określa zachowanie kontenera w monemcie, gdyż jego praca zostanie zakończona. W tym wypadku, w momencie, kiedy kontener przestanie działać z powodu błędy, zostanie automatycznie ponownie powołany do życia.
* ```depends_on``` określa kolejność (zależność), w której kontenery mają zostać uruchomione. Należy zwrócić uwagę, że nie oznacza to, że kontener, który później został utworzony nie będzie pierwszy gotowy do działania (aplikacja może polegać na kontenerze z bazą danych stąd najpierw uruchomimy kontener z tą bazą, natomiast może stać się tak, że naszą aplikacja szybciej skonfiguruje swój kontener niż baza danych i wystąpi problem z połączeniem bazodanowym).
* ```env_file``` dodaje zmienne środowiskowe do kontenera na podstawie zewnętrznego pliku.

Zmienne środowiskowe znajdują się w pliku `.env`, można je także podawać jako parametry do polecenia `docker-compose`.

---

### Uruchomienie skonteneryzowanych i skonfigurowanych aplikacji przy pomocy jednego polecenia

```sh
docker-compose up
```

W przypadku gdyby docker-compose nie był jeszcze zainstalowany razem z dockerem:

```sh
sudo apt-get install docker-compose
```

---

### Przydatne komendy

Zatrzymanie kontenerów i usunięcie kontenerów wraz z obrazami:

```sh
docker-compose down --rmi all
```

---

## 8. Ciągła integracja i wydanie

W celu stworzenia pipelinu CI/CD użyjemy narzędzia GitHub Workflows. Jest to proste w użyciu narzędzie, które pozwala na dużo więcej niż proste pipeliny CI/CD, a do tego pozwala nam trzymać je w postaci kodu na jednym repozytorium wraz z kodem samego projektu. GitHub udostępnia na swoje maszyny budujące, stąd nie musimy się przejmować o infrastrukturę. Kod pipelinu musi znajdować się w plikach z roszerzeniem ```.yml/.yaml``` w katalogu ```.github/workflows/```.

```.github/workflows/<nazwa_pliku>.yml:```

---

### Aktywacja GitHub Action

Aktywujemy tą funkcjonalność poprzez przejście na zakładkę ```Actions``` na naszym sforkowanym repozytorium i kliknięcie przycisku aktywacji.

---

### Dodanie GitHub Secrets do repozytorium

W celu poprawnego korzystania z narzędzia Snyk i serwisu Docker Hub musimy ustawić zmienne przechowujące dane wymagane do uwierzytelniania w tych serwisach jako secrety na platformie GitHub. GitHub Actions będą w trakcie komunikacji z tymi serwisami korzystać z tych secretów i będą one niewidoczne dla osób postronnych. Na platformie GitHub przechodzimy do zakładki ustawień będąc na naszym własnym zforkowanym repozytorium. W ustawieniach przechodzimy do zakładki secretów dla akcji. 

Dodajemy trzy poniższe secrety:
* Nazwa secretu: ```SNYK_TOKEN```. Jego zawartością będzie token, który możemy pozyskać z naszego konta w serwisie Snyk ```https://snyk.io/account/```. 
* Nazwa secretu: ```DOCKERHUB_USERNAME```. Wartość ustawiamy na naszą nazwę użytkownika w serwisie Docker Hub ```https://hub.docker.com/```. Po zalogowaniu do serwisu Docker Hub nasza nazwa użytkownika widnieje w prawym górnym roku.
* Nazwa secretu: ```DOCKERHUB_TOKEN```. Wartość tego sekretu ustawiamy na token, którym możemy pozyskać z naszego konta w serwisie Docker Hub ```https://hub.docker.com/```. Po zalogowaniu do serwisu Docker Hub przechodzimy do zakładki ```Account Settings```, a następnie ```Security``` i tworzymy nasz nowy token, którego wartość kopiujemy do sekretu GitHub.

---

### Automatyzacja testów jednostkowych

```yaml
name: Unit Tests

on:
  push:

jobs:
  unit-testing:
    runs-on: ubuntu-20.04
    
    env:
      FLASK_APP: flaskr/app.py

    steps:
      - uses: actions/checkout@v2

      - name: Set up Python 3.10
        uses: actions/setup-python@v1
        with:
          python-version: '3.10'

      - name: Setup app
        run: pip install -e .[dev]

      - name: Run unit tests
        run: python setup.py test
```

* ```name:``` określa nazwę dla danego workflow/akcji i pod nią będzie on widnieć na Githubie.
* ```on:``` określa sytuację, w której workflow zostanie uruchomiony. W tym przypadku będzie to w momencie, kiedy zostanie wypchnięty commit na zdalne repozytorium: ```push:```.
* ```job``` zawiera zadania, które w ramach tego workflow zostaną uruchomione.
* ```runs-on:``` określa maszynę budującą, na której dany workflow zostanie uruchomiony.
* ```env:``` pozwala na określenie zmiennych środowiskowych, które zostaną ustawione na maszynie budującej
* ```strategy: matrix:``` umożliwia uruchomienie zadania na wielu wersjach wskazanego języka/systemu operacyjnego
* ```steps:``` określa konkretne kroki, które zostaną wykonane w realizacji danego zadania.
* ```uses:``` pozwala na skorzystanie z gotowej akcji zdefiniowanej na zewnętrznym repozytorium.
* ```with:``` umożliwia przekazanie wybranych parametrów do danej akcji.

---

### Automatyczne testowanie przy pomocy narzędzi typu linter (np. pytlint, black)

```yaml
name: Lint code

on:
  push:

jobs:
  lint-code:
    runs-on: ubuntu-20.04

    steps:
      - uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v1
        with:
          python-version: '3.10'

      - name: Lint with pylint
        run: |
          pip install pylint
          pylint --exit-zero flaskr

      - name: Lint with black
        run: |
          pip install black
          python -m black --check .
```

---

### Automatyzacja statycznej analizy kodu

```yaml
name: Static code analysis

on:
  push:
    branches:
      - 'master'
      - 'develop'

jobs:
  snyk-scan:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@master

      - name: Prepare requirements for Snyk
        run: |
          python -m pip install .

      - name: Run Snyk to check for vulnerabilities
        uses: snyk/actions/python-3.8@master
        continue-on-error: true
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
```

---

### Automatyczne budowanie i wdrażanie kontenerów

```yaml
name: Docker build and push

on:
  push:
    branches:
      - 'master'
      - 'develop'

jobs:
  docker:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v3

      - name: Build project with setuptool
        run: |
          python -m pip install .[dev]
          python setup.py bdist_wheel

      - name: Set up QEMU
        uses: docker/setup-qemu-action@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Login to DockerHub
        uses: docker/login-action@v2 
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
          logout: true

      - name: Build and push
        id: docker_build
        uses: docker/build-push-action@v3
        with:
          context: .
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/devops-workshops:develop
```

---

## 9. Wdrożenie aplikacji na platformie Kubernetes

W tej części przygotowany obraz z aplikacją zostanie wdrożony na platformie Kubernetes (k8s).

---

### 9.1. Instalacja Kubernetesa

#### Windows

Po zainstalowaniu Docker Desktop wchodzimy w jego ustawienia (prawym klawiszem myszy na jego ikonie w pasku zadań, następnie wybieramy *settings*) i klikamy na **Enable Kubernetes**. 

O poprawnym uruchomieniu informuje zielony pasek w lewym-dolnym rogu Docker Desktop (powinny być widoczne dwa zielone paski: *Engine Running* świadczący o działaniu silnika Dockera oraz *Kubernetes Running* świadczący o działaniu Kubernetesa).

#### Linux

Wykonujemy poniższe polecenia, które zainstalują **minikube**:

```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 &&\
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Uruchamiamy klaster poleceniem:

```sh
minikube start
```

> **Więcej informacji:**  https://minikube.sigs.k8s.io/docs/start/

#### MacOS

Wykonujemy poniższe polecenia, które zainstalują **minikube**:

```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64 &&\
sudo install minikube-darwin-amd64 /usr/local/bin/minikube
```

> **Więcej informacji:**  https://minikube.sigs.k8s.io/docs/start/

---

### 9.2. Instalacja polecenia kubectl

#### Windows

Polecenie kubectl można zainstalować poprzez manager pakietów **Chocolatey**, który można pobrać z https://chocolatey.org/.
Po jego zainstalowaniu należy wykonać polecenie:

```sh
choco install kubernetes-cli
```

#### Linux

Wykonujemy polecenia:

```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" &&\
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```
    
> **Więcej informacji:**  https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/


#### MacOS

1. Pobranie pliku w postaci binarnej.

Wykonujemy polecenia:

- dla architektury **Intel**:

    ```sh
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"
    ```

- dla architektury **Apple Silicon**:

    ```sh
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/arm64/kubectl"
    ```

2. Instalacja

Wykonujemy polecenia:

```sh
chmod +x ./kubectl &&\
sudo mv ./kubectl /usr/local/bin/kubectl &&\
sudo chown root: /usr/local/bin/kubectl
```

> **Więcej informacji:**  https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/

---

### 9.3. Weryfikacja instalacji

Po instalacji, by zweryfikować poprawność i wersję klienta należy wykonać:

```sh
kubectl version --client
```

Oraz, by zweryfikować wersję zarówno klienta jak i serwera:

```sh
kubectl version
```

Aby wyświetlić stan całego klastra należy wykonać:

```sh
kubectl cluster-info
```

---

### 9.4. Wdrożenie aplikacji

Wdrożenie aplikacji na platformie Kubernetes odbywa się poprzez odpowiednio przygotowane pliki (*deployment files*), w których zdefiniowane są obiekty składowe aplikacji.

W folderze z projektem znajduje się plik `k8s.yaml`, który zawiera definicje obiektów takich typów jak *PersistentVolume*, *PersistentVolumeClaim*, *ConfigMap*, *Service*, *Deployment* i *StatefulSet*.

Za wdrożenie bazy danych odpowiada obiekt typu *StatefulSet*.

Za wdrożenie aplikacji odpowiada obiekt typu *Deployment*, w którym znajduje się definicja obrazu dokerowego aplikacji: `image: devops-workshops:develop`.

> *Można także uzupełnić nazwę obrazu (`devops-workshops:develop`) o nazwę własnego użytkownika serwisu **Dockerhub**, np. dla użytkownika `marcin` obraz powinien mieć nazwę `image: marcin/devops-workshops:develop`. W ten sposób nie użyty zostanie obraz wybudowany lokalnie, lecz ten wybudowany za pomocą Github actions.*

Ostatecznie, by wdrożyć aplikację należy wykonać polecenie:

```sh
kubectl apply -f k8s.yaml
```

> **Uwaga:**  *Polecenie to należy wykonać także po każdej modyfikacji obiektów wdrożenia w pliku `k8s.yaml`*

Aby zweryfikować wdrożenie należy wykonać polecenie:

```sh
kubectl get pods
```

Polecenie to powinno wyświetlić tabelę działających podów (poprawny status to *Running*):

```text
NAME                      READY   STATUS    RESTARTS   AGE
flaskr-6c45555bcf-bh8c2   0/1     Running   0          6s
postgres-0                1/1     Running   0          6s
```

Aby uzyskać szczegółowe informacje o wybranym pod (w tym przypadku o aplikacji) należy wykonać polecenie:

```sh
kubectl describe pod flaskr
```

Polecenie to pokaże także listę zdarzeń (*Events*), jest przydatne przy diagnostyce.

Aby usunąć wdrożenie należy wykonać polecenie:

```sh
kubectl delete -f k8s.yaml
```

> **Więcej informacji:**  https://kubernetes.io/docs/reference/kubectl/cheatsheet/

---

## 10. Ćwiczenia

---

### 10.1. Dodaj nową akcję Github

Dodaj akcję do stworzenia wydania.
Akcja powinna być uruchomiona gdy zostanie stworzony i wypchnięty tag zaczynający się od `v` (np. `v0.1.0`).

Dokumentacja: https://github.com/marvinpinto/action-automatic-releases

Nie zapomnij dodać kroków przygotowania interpreteta Pythona i budowania samej paczki.

Budowanie powinno być wykonane za pomocą polecenia:

```sh
python setup.py bdist_wheel
```

Paczka (plik z rozszerzeniem `.whl`) znajdzie się w folderze `dist`.

Następnie spróbuj dodać i wypchnąć tag komendami Git z linii poleceń:

```sh
git tag v0.1.0 &&\
git push origin develop --tags
```

---

### 10.2. Dodaj healthcheck do usługi w Docker Compose

W tej chwili usługa bazy danych zostanie uruchomiona po usłudze aplikacji. 
Nie gwarantuje to jednak, że baza danych będzie dostępna, gdy aplikacja będzie chciała otworzyć do niej połączenie, inicjalizacja może trwać pewien czas.
Można to rozwiązać przez tzw. *healthcheck*.

Dodaj następujący wpis do `docker-compose.yaml` w serwisie `database`:

```yaml
healthcheck:
  test: ["CMD-SHELL", "pg_isready"]
  interval: 5s
  timeout: 10s
  retries: 3
```

oraz zmień wpis `depends_on` przy serwisie `flask-app`:

```yaml
depends_on:
  database:
    condition: service_healthy
```

Następne uruchom całość poleceniem `docker-compose up`.

---

### 10.3. Dodaj kolejny serwis do Docker Compose

Dodaj nowy serwis (w pliku `docker-compose.yaml`) o nazwie `pgadmin` z obrazem `dpage/pgadmin4`. 
Umożliwi on edycję/przeglądanie bazy danych.

Konieczne będzie zdefiniowanie zmiennych środowiskowych, niezbędnych do zalogowania do panelu administracyjnego:
- `PGADMIN_DEFAULT_EMAIL`: adres email do logowania.
- `PGADMIN_DEFAULT_PASSWORD`: wybrane hasło.

Więcej informacji: https://www.pgadmin.org/docs/pgadmin4/latest/container_deployment.html

Należy także pamiętać o sekcji `ports`: serwis działa domyślnie na porcie 80, zmapuj go do portu 5050 hosta lub innego wolnego.

Uruchom zaktualizowany stos aplikacji poleceniem `docker-compose up`. 
Używając przeglądarki zaloguj się do panelu administracyjnego, dziajającego na wybranym porcie. 

---

### 10.4. Zabezpiecz URI do bazy danych we wdrożeniu Kubernetesa

Obecnie URI do bazy danych jest podany w pliku `k8s.yaml` w postaci czystego tekstu. 

Aby zabezpieczyć wrażliwe dane można użyć obiektu typu *Secret*. 

Przechowaj w obiekcie URI dla bazy danych, który następnie zostanie wstrzyknięty jako zmienna środowiskowa.

Stwórz taki obiekt używając `kubectl` o nazwie `database-data`:

```sh
kubectl create secret generic database-data --from-literal=DATABASE_URI=postgresql://prod_user:prod_password@postgres:5432/prod_db
```

Następnie użyj go w deploymencie (`k8s.yaml`).

Dokumentacja: https://kubernetes.io/docs/concepts/configuration/secret/

Sprawdź czy został utworzony poleceniem:

```sh
kubectl get secret database-data
```

Można także edytować secret poleceniem:

```sh
kubectl edit secrets database-data
```

Przeprowadź wdrożenie za pomocą polecenia `kubectl apply -f k8s.yaml` i sprawdź działanie aplikacji.
