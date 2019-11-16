## Czy będzie wymagać użycia kontenerów typu Init? jeśli tak to dlaczego?
Raczej nie.
## Czy będzie wymagać użycia wolumenów emptyDir?
Raczej nie. 
Nie przechowuje danych tymczasowych.
## Czy posiada endpointy mogące służyć jako probe dla Health checks? Jeśli nie to czy można je zaimplementować bez problemu?
Aktualnie nie posiada ale można takie dorobić. 
livenessProbe: po http
readinessProbe: do mogoDB pewnie po TCP
## Jaka klasa QoS będzie do niej pasować najlepiej? Dlaczego?
Na tym etapie wybrałbym domyślny BestEffort
## Czy będą potrzebne postStart i preStop hook? Jeśli tak to dlaczego?
Raczej nie.
## Czy aplikacja obsługuje SIG_TERM poprawnie? Jeśli nie to jak można to obejść?
Tak obsługuje.
## Czy domyślny czas na zamknięcie aplikacji w Pod jest wystarczający?
Raczej tak.