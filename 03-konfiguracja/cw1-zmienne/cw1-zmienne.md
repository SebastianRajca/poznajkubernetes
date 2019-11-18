## Korzystając z materiałów z lekcji przetestuj działania zmienne środowiskowe w praktyce.
- Wykorzystaj proste zmienne środowiskowe
- Wykorzystaj w args zmienne środowiskowe
- Skorzystaj z możliwości przekazania informacji o pod poprzez zmienne środowiskowe

Przykładowy yaml:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  restartPolicy: Never
  containers:
  - name: busybox
    image: busybox
    command: ["echo"]
    args: ["Moje imię: $(MY_NAME), nazwa poda: $(POD_NAME),  ip poda: $(POD_IP)"]   
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
    resources: {}
```

Wynik:

```
$ k logs busybox 
Moje imię: Sebastian, nazwa poda: busybox,  ip poda: 10.1.0.203
```
