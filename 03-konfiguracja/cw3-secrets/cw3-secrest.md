# Część 1 – stworzenie własnego prywatnego repozytorium kontenerów

Mam repozytorium w Azure:

```
$ docker login crsebastian.azurecr.io
...
...
Login Succeeded
```

Taguje obraz:

```
$ docker tag poznajkubernetes/pkad:blue crsebastian.azurecr.io/pkad-private:blue
```
Wypycham obraz:

```
$ docker push crsebastian.azurecr.io/pkad-private:blue
```

# Część 2 – wykorzystanie obrazu z prywatnego repozytorium
Utwórz Pod, który ściągnie obraz z prywatnego repozytorium. Musisz wykonać następujące kroki:

### Utworzyć secret typu `kubernetes.io/dockerconfigjson` na podstawie pliku `.docker/config.json`.

```
$ kubectl create secret generic crcreg --from-file=.dockerconfigjson=/home/user/.docker/config.json --type=kubernetes.io/dockerconfigjson

$ k get secrets 
NAME                  TYPE                                  DATA   AGE
crcreg                kubernetes.io/dockerconfigjson        1      71s
```

```
$ k get secrets crcreg -o yaml
apiVersion: v1
data:
  .dockerconfigjson: 
  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
kind: Secret
metadata:
  creationTimestamp: "2019-11-24T05:38:28Z"
  name: crcreg
  namespace: default
  resourceVersion: "163849"
  selfLink: /api/v1/namespaces/default/secrets/crcreg
  uid: a48d80cc-0e7c-11ea-a731-00155d3d8113
type: kubernetes.io/dockerconfigjson
```

### Użyć go w definicji Pod. 

```
apiVersion: v1
kind: Pod
metadata:
  name: crpkad
spec:
  restartPolicy: Never
  containers:
  - name: crpkad
    image: crsebastian.azurecr.io/pkad-private:blue
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
    ports:
      - containerPort: 8080
  imagePullSecrets:
    - name: crcreg
```

### Wgrać Pod do klastra.

```
$ k apply -f pod01-mod03-cw3.yaml 
pod/crpkad created
```
```
$ k get pods
NAME     READY   STATUS    RESTARTS   AGE
crpkad   1/1     Running   0          12s
```

# Część 3 – stwórz secret i wykorzystaj go w Pod
Mając już obraz z prywatnego repozytorium, stwórz 2 rodzaje secret: --from-literal i --from-file, używając polecenia kubectl create. 
```
kubectl create secret generic secret-literal --from-literal=user=sebastian --from-literal=password=1234abcd
```

```
$ kubectl create secret generic secret-literal --from-literal=user=sebastian --from-literal=password=1234abcd
secret/secret-literal created
```
```
$ k get secrets 
NAME                  TYPE                                  DATA   AGE
crcreg                kubernetes.io/dockerconfigjson        1      28m
secret-file           Opaque                                1      5s
secret-literal        Opaque                                2      2m2s
```

Gdy będziesz miał już je utworzone, spróbuj wykorzystać je jako pliki i/lub zmienne środowiskowe

definicja poda:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo-secret
  labels:
    name: demo-secret
spec:
  containers:
  - name: demo-secret
    image: crsebastian.azurecr.io/pkad-private:blue
    envFrom:
      - secretRef:
          name: secret-literal      
      - secretRef:
          name: secret-file
    volumeMounts:
      - mountPath: /secret/
        name: secret-volume
        readOnly: true
    resources: {}
    ports:
      - containerPort: 8080
  volumes:
    - name: secret-volume
      secret:
          secretName: secret-file
```

```
$ k apply -f pod02-mod03-cw3.yaml 
pod/demo-secret created
```

```
$ kubectl exec demo-secret -- printenv
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=demo-secret
user=sebastian
password=1234abcd
secrets.txt=haslo=1234567890abc
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
HOME=/
```

```
$ kubectl exec demo-secret -- ls /secret
secrets.txt
```

```
$ kubectl exec demo-secret -- cat /secret/secrets.txt
haslo=1234567890abc
```