# Praca domowa moduł 1

# Aplikacja napisana we framework-u Meteor.js (full-stack JavaScript framework). Posiada zintegrowany serwer Node.js oraz bazę danych MognoDB. Aplikacja działa w trybie publish/subscribe. Obecnie hostowana na VM-kach.

## Jakie praktyki z 12Factor mój projekt aktualnie spełnia? / Jakich praktyk z 12Factor nie spełnia?

### I. Codebase

Spełnia: całość kodu jest repozytorium GIT

### II. Zależności 
Spełnia: zależności przechowywane w package.json

### III. Konfiguracja / Przechowuj konfigurację w środowisku
Spełnia: konfigurację przechowuje w zmiennych środowiskowych

### IV. Usługi wspierające / Traktuj usługi wspierające jako przydzielone zasoby
Na razie nie spełnia: baza MongoDB jest częścią framewroka. 
Chciałbym ja jednak oddzielić i podpiąć jako usługę w klastrze. 
PS. Liczę że w dalszych modułach zahaczymy o dobre praktyki przechowywania danych no-sql w klastrze.

### V. Buduj, publikuj, uruchamiaj / Oddzielaj etap budowania od uruchamiania
Częściowo spełnia: Obecnie aplikacja jest budowana z repo git, pakowana, wysyłana oraz uruchamiana na produkcji skryptem .sh. 
Niestety nie jest to tagowane, nie mam możliwości prostego cofnięcia wersji, gdyby cos poszło nie tak konieczna była by przerwa w działaniu aplikacji.
Meteor ma za to fajna funkcję że potrafi automatycznie wypchnąć zmiany do klientów, wiec przy drobnych zamianach klient nie zauważy release-u.
Chciałbym  jednak wdrożyć CI/CD aby zarządzać tym procesem.
### VI. Procesy / Uruchamiaj aplikację jako jeden lub więcej bezstanowych procesów
Spełnia częściowo: Aplikacja jest kompilowana do jednego pliku js.

### VII. Przydzielanie portów / Udostępniaj usługi przez przydzielanie portów
Spełnia: Meteor udostępnia aplikacje na wybranym porcie.

### VIII. Współbieżność / Skaluj przez odpowiednio dobrane procesy
Na razie nie spełnia: Ze względu na użycie frameworka może być ciężko wydzielić z niej kilka procesów.
Są pewne rozwiązania oparte o RedisOplog jako cache dla systemu pub/sub meteora. Samą aplikację można wtedy skalować na wielu hostach.
### IX. Zbywalność / Zwiększ elastyczność pozwalając na szybkie uruchamianie i zatrzymywanie aplikacji
Na tym etapie nie spełnione.

### X. Jednolitość środowisk / Utrzymuj konfigurację środowisk jak najbardziej zbliżoną do siebie
Obecnie development odbywa się na vm-ce jak najbardziej zbliżonej do produkcyjnej. Ale chcę przejść na konteneryzację.

### XI. Logi / Traktuj logi jako strumień zdarzeń
Spełnia częściowo: Logi są obecnie wyrzucane na konsolę ale nie są nigdzie składowane.

### XII. Zarządzanie aplikacją / Uruchamiaj zadania administracyjne jako jednorazowe procesy
Na tym etapie nie spełnione.

## Czy mogę tak zmodyfikować projekt, by spełniał wszystkie praktyki 12Factor?
Przy odpowiednim nakładzie pracy powinno to być możliwe.
## Czy jest sens spełniać wszystkie praktyki 12Factor w aktualnym projekcie?
Tak
## Czy architektura rozwiązania umożliwi konteneryzację? 
Tak
## Dlaczego tak?
Jest to aplikacja oparta w gruncie rzeczy o node.js i mongodb które są dostępne w rozwiązaniach kontenerowych od dawna.