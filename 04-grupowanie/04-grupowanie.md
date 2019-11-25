## Wykonaj podstawowe operacje na etykietach imperatywnie. Takie operacje będą przydatne w późniejszych częściach szkolenia jak na przykład trzeba będzie przeanalizować niedziałający Pod albo przekierować ruch na inne Pody.

tworzę trzy pody

```
kubectl run pkad-a --image=poznajkubernetes/pkad --restart=Never
kubectl run pkad-b --image=poznajkubernetes/pkad --restart=Never
kubectl run busy-c --image=busybox --restart=Never
kubectl run busy-d --image=busybox --restart=Never
```

- Dodaj etykietę
```
kubectl label pods pkad-a env=prod
kubectl label pods pkad-b env=test
kubectl label pods busy-c env=prod
kubectl label pods busy-d env=stg
```
```
$ kubectl get pods -L env
NAME     READY   STATUS      RESTARTS   AGE     ENV
busy-c   0/1     Completed   0          3m41s   prod
busy-d   0/1     Completed   0          3m39s   stg
pkad-a   1/1     Running     0          3m56s   prod
pkad-b   1/1     Running     0          3m41s   test
```
- Dodaj etykietę do wszystkich zasobów na raz
  
```bash
$ kubectl label pods --all version=1.0
pod/busy-c labeled
pod/busy-d labeled
pod/pkad-a labeled
pod/pkad-b labeled

$ kubectl get pods -L env,version
NAME     READY   STATUS      RESTARTS   AGE     ENV    VERSION
busy-c   0/1     Completed   0          5m49s   prod   1.0
busy-d   0/1     Completed   0          5m47s   stg    1.0
pkad-a   1/1     Running     0          6m4s    prod   1.0
pkad-b   1/1     Running     0          5m49s   test   1.0

```
- Zaktualizuj etykietę
```
$kubectl label pods pkad-a --overwrite version=2.0

$ kubectl get pods -L env,version
NAME     READY   STATUS      RESTARTS   AGE     ENV    VERSION
busy-c   0/1     Completed   0          7m37s   prod   1.0
busy-d   0/1     Completed   0          7m35s   stg    1.0
pkad-a   1/1     Running     0          7m52s   prod   2.0
pkad-b   1/1     Running     0          7m37s   test   1.0
```

- Usuń etykietę
```
kubectl label pods pkad-b env-

$ kubectl get pods -L env,version
NAME     READY   STATUS      RESTARTS   AGE     ENV    VERSION
busy-c   0/1     Completed   0          8m42s   prod   1.0
busy-d   0/1     Completed   0          8m40s   stg    1.0
pkad-a   1/1     Running     0          8m57s   prod   2.0
pkad-b   1/1     Running     0          8m42s          1.0
```

## Stwórz trzy Pody z czego dwa posiadające po dwie etykiety: app=ui i env=test oraz app=ui i env=stg, trzeci bez etykiet

```
kubectl run pkad-1 --image=poznajkubernetes/pkad --restart=Never
kubectl run pkad-2 --image=poznajkubernetes/pkad --restart=Never
kubectl run pkad-3 --image=poznajkubernetes/pkad --restart=Never

kubectl label pods pkad-1 app=ui env=test
kubectl label pods pkad-2 app=ui env=stg

$ kubectl get pods -L app,env
NAME     READY   STATUS    RESTARTS   AGE     APP   ENV
pkad-1   1/1     Running   0          2m32s   ui    test
pkad-2   1/1     Running   0          2m31s   ui    stg
pkad-3   1/1     Running   0          2m31s    
```
- Wybierz wszystkie Pody które mają etykietę env zdefiniowaną

```
$ kubectl get pods --selector env
NAME     READY   STATUS    RESTARTS   AGE
pkad-1   1/1     Running   0          3m11s
pkad-2   1/1     Running   0          3m10s
```

- Wybierz wszystkie Pody które nie mają etykiety env zdefiniowanej

```
$ kubectl get pods --selector '!env'
NAME     READY   STATUS    RESTARTS   AGE
pkad-3   1/1     Running   0          4m15s
```

- Wybierz Pody które mają app=ui ale nie znajdują się w env=stg

```
$ kubectl get pods -l env!=stg,app=ui
NAME     READY   STATUS    RESTARTS   AGE
pkad-1   1/1     Running   0          6m28s
```

- Wybierz Pody których env znajduje się w przedziale stg i demo

```
$ kubectl get pods -l 'env in (demo, stg)'
NAME     READY   STATUS    RESTARTS   AGE
pkad-2   1/1     Running   0          7m22s
```

## Z wcześniej stworzonych Podów:
- Wybierz i wyświetl tylko nazwy Poda

```
$ kubectl get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\n"}{end}'
pkad-1
pkad-2
pkad-3
```
- Posortuj widok po dacie ostatniej aktualizacji Poda

```
$ kubectl get pods --sort-by=.metadata.creationTimestamp
NAME     READY   STATUS    RESTARTS   AGE
pkad-1   1/1     Running   0          9m49s
pkad-2   1/1     Running   0          9m48s
pkad-3   1/1     Running   0          9m48s
```
- Wybierz tylko i wyłączenie te Pody które nie są w fazie Running
```
$ kubectl get pods --field-selector status.phase!=Running
No resources found.
```
