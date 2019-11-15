# Ćwiczenie 1

1. Utwórz definicje (plik YAML) pod z obrazem busybox za pomocą polecenia kubectl run z opcjami lub używając edytora tekstowego. Pamiętaj o opcjach --dry-run oraz -o yaml

```
kubectl run busybox --image=busybox --generator=run-pod/v1  --dry-run -o yaml > pod.yaml
```

```yml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - image: busybox
    name: busybox
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

2. Używając polecenia kubectl create stwórz Pod na podstawie definicji z punktu 1.

```
kubectl create -f pod.yaml
```

3. Sprawdź status tworzenia Pod za pomocą get. Możesz też ustawić w osobnym oknie sprawdzanie „ciągłe” z opcją -w czyli --watch i zostawić je do końca ćwiczeń.

```
$ k get po -w
```

```
NAME      READY   STATUS    RESTARTS   AGE
busybox   0/1     Pending   0          0s
busybox   0/1     Pending   0          0s
busybox   0/1     ContainerCreating   0          0s
busybox   0/1     Completed           0          7s
```

4. Zastanów się dlaczego Pod przeszedł w stan Completed.

Nie było procesu który by podtrzymał pracę kontenera

# Ćwiczenie 2

1. Korzystając ze stanu klastra z ćwiczenia 1 oraz pliku YAML, przystąp do ćwiczenia.

2. Popraw plik YAML dodając command tak by Pod nie kończył od razu swojej pracy. Np: dodaj lub zmień tag w obrazie. Poprawne tag dla busybox znajdziesz na stronie: https://hub.docker.com/_/busybox

```yml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - image: busybox:1
    name: busybox
    resources: {}
    command:
      - sleep
      - "3600"
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

3. Użyj polecenia kubectl create by wgrać nową definicję.

```
Error from server (AlreadyExists): error when creating "pod2.yaml": pods "busybox" already exists
```

4. Zastanów się dlaczego nie działa.

Bark opcji --save-config=true w trackie tworzenia poda.

5. Usuń starą definicję za pomocą kubectl delete.

```
kubectl delete pod busybox
```

# Ćwiczenie 3

1. Utwórz w YAML definicję Pod, który nie kończy od razu swojej pracy. Możesz skorzystać z pliku stworzonego YAML w ćwiczeniu 2.

```yml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - image: busybox:1
    name: busybox
    resources: {}
    command:
      - sleep
      - "15"
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

2. Wgraj go na klaster używając polecenia create.

```
kubectl create -f pod2.yaml --save-config=true 
```

3. Wykonaj modyfikację pliku np: zmień czas dla komendy sleep
Spróbuj wgrać modyfikacje na klaster używając create lub apply.
```
$ kubectl create -f pod2.yaml --save-config=true 
Error from server (AlreadyExists): error when creating "pod2.yaml": pods "busybox" already exists
```
```
$ kubectl replace -f pod2.yaml
The Pod "busybox" is invalid: spec: Forbidden: pod updates may not change fields other than `spec.containers[*].image`, `spec.initContainers[*].image`, `spec.activeDeadlineSeconds` or `spec.tolerations` (only additions to existing tolerations)
```

4. Zastanów się i opisz na grupie kiedy warto używać create, a kiedy apply.

| Kubectl apply | Kubectl create |
| --- | --- |
| It directly updates in the current live source, only | It first deletes the resources and then creates it from the file provided. |
| The file used in apply can be an incomplete spec  | The file used in create should be complete |
| Apply works only on some properties of the resources  | Create works on every property of the resources |
| You can apply a file that changes only an annotation, without specifying any other properties of the resource. | If you will use the same file with a replace command, the command would fail, due to the missing information. |


# Ćwiczenie 4

1. Posprzątaj swój klaster tak by nie było na nim stworzonych przez Ciebie Pod.
```
$ kubectl delete pod busybox 
```
2. Pamiętaj o get i delete.
```
$ kubectl get pods
No resources found.
```
3. Czy masz pomysł jak to zautomatyzować?

usuń wszystkie pody
```
kubectl delete pods --all
```
usuń pody pasujące do definicji w pod.json
```
kubectl delete -f ./pod.json
```