## Przetestuj działanie Service Discovery korzystając ze swojej aplikacji albo helloapp.

Utwórz dwa namespaces

```bash
$ k create ns module-05-ns1
namespace/module-05-ns1 created

$ k create ns module-05-ns2
namespace/module-05-ns2 created

$ k get ns
NAME              STATUS   AGE
default           Active   29d
docker            Active   29d
kube-node-lease   Active   29d
kube-public       Active   29d
kube-system       Active   29d
module-05-ns1     Active   12s
module-05-ns2     Active   6s
```

W każdym namespaces umieć pod i serwis

APP:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: helloapp
  labels:
    app: helloapp
    ver: v1
spec:
  containers:
  - name: helloapp
    image: poznajkubernetes/helloapp:svc
    ports:
    - containerPort: 8080
      name: http
      protocol: TCP
---
kind: Service
apiVersion: v1
metadata:
  name: helloapp-port
spec:
  selector:
    app: helloapp
  type: ClusterIP
  ports:
  - name: http
    port: 80
    targetPort: 8080
    protocol: TCP
```
```bash
$ k apply -f cw2-app.yaml -n module-05-ns1
pod/helloapp created
service/helloapp-port created

$ k get pods -n module-05-ns1 -o wide
NAME       READY   STATUS    RESTARTS   AGE   IP           NODE             NOMINATED NODE   READINESS GATES
helloapp   1/1     Running   0          52s   10.1.0.245   docker-desktop   <none>           <none>

$ k get svc -n module-05-ns1 -o wide
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE   SELECTOR
helloapp-port   ClusterIP   10.107.131.10   <none>        80/TCP    71s   app=helloapp

$ k apply -f cw2-app.yaml -n module-05-ns2
pod/helloapp created
service/helloapp-port created

$ k get pods -n module-05-ns2 -o wide
NAME       READY   STATUS    RESTARTS   AGE   IP           NODE             NOMINATED NODE   READINESS GATES
helloapp   1/1     Running   0          22s   10.1.0.246   docker-desktop   <none>           <none>

$ k get svc -n module-05-ns2 -o wide
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE   SELECTOR
helloapp-port   ClusterIP   10.108.74.241   <none>        80/TCP    48s   app=helloapp
```

Przetestuj działanie Service Discovery z wykorzystaniem curl i nslookup. Jeśli używasz swojej aplikacji wywołaj endpointy pomiędzy aplikacjami.

```bash
$ k exec -it tools sh
/ # nslookup helloapp-port.module-05-ns1
Server:         10.96.0.10
Address:        10.96.0.10#53
Name:   helloapp-port.module-05-ns1.svc.cluster.local
Address: 10.107.131.10

/ # curl helloapp-port.module-05-ns1.svc.cluster.local
Cześć, 🚢 =>  helloapp

/ # nslookup helloapp-port.module-05-ns2
Server:         10.96.0.10
Address:        10.96.0.10#53
Name:   helloapp-port.module-05-ns2.svc.cluster.local
Address: 10.108.74.241

/ # curl helloapp-port.module-05-ns2.svc.cluster.local
Cześć, 🚢 =>  helloapp

```