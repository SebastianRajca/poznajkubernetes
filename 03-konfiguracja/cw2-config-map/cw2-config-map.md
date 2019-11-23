# Praca z ConfigMap
### Stwórz ConfigMap wykorzystując kubectl
- Załącz do niej przynajmniej dwie proste wartości (literal)
- Załącz do niej klucz: 123_TESTING z dowolną wartością
- Załącz do niej klucz: TESTING-123 z dowolną wartością
- Załącz do niej klucz: TESTINGz dowolną wartością

```
$ kubectl create cm cm01-mod03-cw2 --from-literal=testKey=KluczTest --from-literal=testKey2=Klucz2Test --from-literal=123_TESTING=Test123 --from-literal=TESTING-123=Test-123 --from-literal=TESTING=Test

configmap/cm01-mod03-cw2 created
```
```
$ k get cm cm01-mod03-cw2 -o yaml
```
``` yaml
apiVersion: v1
data:
  123_TESTING: Test123
  TESTING: Test
  TESTING-123: Test-123
  testKey: KluczTest
  testKey2: Klucz2Test
kind: ConfigMap
metadata:
  creationTimestamp: "2019-11-23T18:18:34Z"
  name: cm01-mod03-cw2
  namespace: default
  resourceVersion: "77791"
  selfLink: /api/v1/namespaces/default/configmaps/cm01-mod03-cw2
  uid: a976677b-0e1d-11ea-860e-00155d0b610d
```

### Stwórz drugą ConfigMap wykorzystując kubectl
- Załącz do niej dwie takie same klucze i ale różne wartości
- Jeden plik normalnie
- Oraz jeden plik z inną nazwą klucza niż nazwa pliku

```
$ kubectl create cm cm02-mod03-cw2 --from-literal=testKey=KluczTestDwa --from-literal=testKey2=Klucz2TestDwa --from-literal=123_TESTING=Test123Dwa --from-literal=TESTING-123=Test-123Dwa --from-literal=TESTING=TestDwa --from-file=cw2-json1.json --from-file=plik_json=cw2-json2.json

configmap/cm02-mod03-cw2 created
```

```
$ k get cm cm02-mod03-cw2 -o yaml
```
```yaml
apiVersion: v1
data:
  123_TESTING: Test123Dwa
  TESTING: TestDwa
  TESTING-123: Test-123Dwa
  cw2-json1.json: |-
    {
      "fruit": "Apple",
      "size": "Large",
      "color": "Red"
    }
  plik_json: |-
    {
        "firstName": "Jan",
        "lastName": "Kowalski",
        "age": 24,
        "address": {
            "streetAddress": "126",
            "city": "Waszawa",
            "state": "MAZ",
            "postalCode": "00-000"
        },
        "phoneNumbers": [
            { "type": "mob", "number": "1321346546" }
        ]
    }
  testKey: KluczTestDwa
  testKey2: Klucz2TestDwa
kind: ConfigMap
metadata:
  creationTimestamp: "2019-11-23T18:51:29Z"
  name: cm02-mod03-cw2
  namespace: default
  resourceVersion: "80191"
  selfLink: /api/v1/namespaces/default/configmaps/cm02-mod03-cw2
  uid: 42ac7818-0e22-11ea-860e-00155d0b610d
```

### Stwórz trzecią ostatnią ConfigMapę wykorzystując kubectl
- zrób tak by załączyć pliki o rozmiarach ~20KB, ~30KB, ~40KB i ~50KB

wygenerowałem pliki komendą:
```
$ perl -e 'print "a" x 20480' > file.20k
$ perl -e 'print "b" x 30720' > file.30k
$ perl -e 'print "c" x 40960' > file.40k
$ perl -e 'print "d" x 51200' > file.50k
```

```
$ kubectl create cm cm03-mod03-cw2 --from-file=file_20k=file.20k --from-file=file_30k=file.30k --from-file=file_40k=file.40k --from-file=file_50k=file.50k

configmap/cm03-mod03-cw2 created
```

### Wyeksportuj wszystkie stworzone ConfigMapy do yamli.

```
$ k get cm cm01-mod03-cw2 -o yaml > cm01-mod03-cw2.yaml
```
```
$ k get cm cm02-mod03-cw2 -o yaml > cm02-mod03-cw2.yaml
```
```
$ k get cm cm03-mod03-cw2 -o yaml > cm03-mod03-cw2.yaml
```

### Co się stanie gdy nadamy taki sam klucz? Czego Ty byś się spodziewał?
W obrębie jednej config mapy nazwa klucza musi być unikalna

### Czy można nadać dowolną nazwę klucza w ConfigMap?
Nie nie można tylko takie **'[-._a-zA-Z0-9]+'**
```
error: "test#Key" is not a valid key name for a ConfigMap: a valid config key must consist of alphanumeric characters, '-', '_' or '.' (e.g. 'key.name',  or 'KEY_NAME',  or 'key-name', regex used for validation is '[-._a-zA-Z0-9]+')
```

# Zmienne środowiskowe
### Będziemy korzystać z naszych ConfigMap z poprzedniej sekcji. Ćwiczenia te polegają na obserwowaniu wyniku akcji i zastanowieniu się dlaczego wynik jest taki a nie inny.

### Dla Poda możesz skorzystać z własnego obrazu lub z obrazu poznajkubernetes/pkad

- Wczytaj wszystkie klucze z pierwszej ConfigMapy do Poda jako zmienne środowiskowe. Zweryfikuj dokładnie zmienne środowiskowe. Jaki wynik został uzyskany i dlaczego taki?

pod01-mod03-cw2.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pkad
spec:
  restartPolicy: Never
  containers:
  - name: pkad
    image: poznajkubernetes/pkad
    env:
    - name: MY_NAME
      value: "Sebastian"
    - name: POD_NAME
      valueFrom:
        fieldRef:
          fieldPath: metadata.name
    - name: POD_IP
      valueFrom:
        fieldRef:
          fieldPath: status.podIP
    envFrom:
      - configMapRef:
          name: cm01-mod03-cw2          
    resources: {}
```
```
$ k apply pod -f pod01-mod03-cw2.yaml

pod/pkad created
```

```
$ kubectl exec pkad -- printenv

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=pkad
POD_NAME=pkad
POD_IP=10.1.0.42
TESTING=Test
TESTING-123=Test-123
testKey=KluczTest
testKey2=Klucz2Test
MY_NAME=Sebastian
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
HOME=/
```

nie ma zmiennej:
```
$ k describe pod pkad

... Keys [123_TESTING] from the EnvFrom configMap default/cm01-mod03-cw2 were skipped since they are considered invalid environment variable names.
```

- Wczytaj wszystkie klucze z trzeciej ConfigMapy do Poda jako zmienne środowiskowe. Zweryfikuj dokładnie zmienne środowiskowe. Jaki wynik został uzyskany i dlaczego taki?


pod02-mod03-cw2.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pkad2
spec:
  restartPolicy: Never
  containers:
  - name: pkad2
    image: poznajkubernetes/pkad
    env:
    - name: MY_NAME
      value: "Sebastian"
    - name: POD_NAME
      valueFrom:
        fieldRef:
          fieldPath: metadata.name
    - name: POD_IP
      valueFrom:
        fieldRef:
          fieldPath: status.podIP
    envFrom:
      - configMapRef:
          name: cm03-mod03-cw2          
    resources: {}
```
```
$ k apply pod -f pod02-mod03-cw2.yaml

pod/pkad created
```

Miałem problemy bo najpierw wygenerowałem sobie pliki binarne i niestety pod nie chciał wstać. 
Potem poprawiłem na pliki tekstowe oraz od razu nazwałem zmienne środowiskowe poprawnie.
Wszystko zadziałało pod stoi zmienne są.

### Odpowiedz sobie na pytania:
- Co ma pierwszeństwo: zmienna środowiskowa zdefiniowana w ConfiMap czy w Pod?

Próbowałem nadpisać tą w pod ale wygląda na to że ma pierwszeństwo.

- Czy kolejność definiowania ma znaczenie (np.: env przed envFrom)?

Ma znaczenie w przypadku duplikatów.

- Jak się ma kolejność do dwóch różnych ConfigMap?

W przypadku duplikatów zostaną nadpisane zmienne o takich samych kluczach.

# Wolumeny
- Wykorzystując drugą ConfigMapę stwórz Pod i wczytaj wszystkie pliki do katalogu wybranego przez siebie katalogu



- Wczytaj do wolumenu tylko i wyłącznie pliki powyżej 30KB z trzeciej ConfigMapy