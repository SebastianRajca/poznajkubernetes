### Bazując na wiedzy z poprzednich modułów możesz teraz przeanalizować w jaki sposób udostępniać aplikacje sieciowo w klastrze i po za klastrem. Jeżeli nie masz swojego systemu, to wykonaj mentalne ćwiczenie „jakie wartości fajnie by było mieć”.

#### Czy będzie korzystać z Service Discovery? Jeśli tak to jakiego? Dlaczego?
  
Wbudowany w Kubernetes DNS wydaje się wystarczający dla moich zastosowań chyba że w dalszej części kursu pokażecie inne rozwiązania.

#### Jakie typy serwisu wykorzysta do Services wykorzystasz? Dlaczego?

ClusterIP – do większości komunikacji wewnątrz klastra.
NodePort – na tym etapie kursu widzę zastosowanie do testów wewnętrznych 
LoadpBalancer – do wystawienia na świat

#### Do publikacji aplikacji użyjesz NodePort czy LoadBalancer? Dlaczego?

LoadBalancer – planuje umieścić aplikacje na klastrze w Azure więc wydaje mi się to naturalnym rozwiązaniem.

