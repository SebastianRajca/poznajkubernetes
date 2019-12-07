# Ćwiczenie 1

Korzystając z wiedzy z lekcji przetestuj następujące scenariusze komunikacji:

- container-to-container w Pod. Wykorzystaj do tego nginx.

POD:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:alpine
    resources: {}
    ports:
    - containerPort: 80
      name: http
      protocol: TCP
  - name: tools
    image: giantswarm/tiny-tools
    resources: {}
    command: ["/bin/sh"]
    args: ["-c", "sleep 3600"]
```
```bash
$ k apply -f cw1-container-to-container.yaml 
pod/nginx created

$ k get pod
NAME    READY   STATUS    RESTARTS   AGE
nginx   2/2     Running   0          34s

$ k exec -it nginx -c tools sh
/ # curl localhost
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

- Komunikacja pomiędzy Podami – Pod-to-Pod. Wykorzystaj do tego nginx.

POD:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:alpine
    resources: {}
    ports:
    - containerPort: 80
      name: http
      protocol: TCP
---
apiVersion: v1
kind: Pod
metadata:
  name: tools
  labels:
    app: tools
spec:
  containers:
  - name: tools
    image: giantswarm/tiny-tools
    resources: {}
    command: ["/bin/sh"]
    args: ["-c", "sleep 3600"]
```
```bash
$ k apply -f cw1-pod-to-pod.yaml
pod/nginx created
pod/tools created

$ k get pod -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP           NODE             NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          12s   10.1.0.232   docker-desktop   <none>           <none>
tools   1/1     Running   0          12s   10.1.0.233   docker-desktop   <none>           <none>

$ k exec -it  tools sh
/ # curl 10.1.0.232
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
/ # 
```
- Wykorzystaj nginx i wystaw go za pomocą serwisu ClusterIP w środku klastra.

SERVICE
```yaml
kind: Service
apiVersion: v1
metadata:
  name: nginx-port
spec:
  selector:
    app: nginx
  type: ClusterIP
  ports:
  - name: http
    port: 80
    targetPort: 80
    protocol: TCP
```

```bash
$ k apply -f cw1-svc-clusterip-port.yaml 
service/nginx-port created

$ k get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   28d
nginx-port   ClusterIP   10.111.246.17   <none>        80/TCP    8s

$ k exec -it  tools sh
/ # curl 10.111.264.17
curl: (6) Could not resolve host: 10.111.264.17
/ # curl 10.111.246.17
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
/ # 
```



- Wykorzystaj nginx i wystaw go na świat za pomocą serwisu NodePort w dwóch opcjach: bez wskazywania portu dla NodePort i ze wskazaniem.

NODE PORT:
```yaml
kind: Service
apiVersion: v1
metadata:
  name: nodeport-nginx
spec:
  selector:
    app: nginx
  type: NodePort
  ports:
  - name: http
    port: 80
    targetPort: 80
    protocol: TCP
```

```bash
$ k apply -f cw1-svc-nodeport.yaml 
service/nodeport-nginx created

$ k get svc -o wide
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE   SELECTOR
kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP        28d   <none>
nodeport-nginx   NodePort    10.110.28.251   <none>        80:31018/TCP   5s    app=nginx

$ curl localhost:31018
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

NODE PORT - NAMED PORT:
```yaml
kind: Service
apiVersion: v1
metadata:
  name: nodeport-nginx-named
spec:
  selector:
    app: nginx
  type: NodePort
  ports:
  - name: http
    port: 80
    targetPort: 80
    protocol: TCP
    nodePort: 32000
```
```bash
$ k apply -f cw1-svc-nodeport-named.yaml
service/nodeport-nginx-named created

$ k get svc
NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes             ClusterIP   10.96.0.1       <none>        443/TCP        28d
nodeport-nginx         NodePort    10.110.28.251   <none>        80:31018/TCP   9m55s
nodeport-nginx-named   NodePort    10.110.170.58   <none>        80:32000/TCP   6s

$ curl localhost:32000
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

- Wykorzystaj nginx i wystaw go na świat za pomocą serwisu typu LoadBalancer,

LOAD BALANCER
```yaml
kind: Service
apiVersion: v1
metadata:
  name: loadbalancer-nginx
spec:
  selector:
    app: nginx
  type: LoadBalancer
  ports:
  - name: http
    port: 80
    targetPort: 80
    protocol: TCP
```

```bash
$ k apply -f cw1-svc-loadbalancer.yaml 
service/loadbalancer-nginx created

$ k get svc
NAME                   TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes             ClusterIP      10.96.0.1       <none>        443/TCP        28d
loadbalancer-nginx     LoadBalancer   10.106.7.71     localhost     80:30069/TCP   4s
nodeport-nginx         NodePort       10.110.28.251   <none>        80:31018/TCP   19m
nodeport-nginx-named   NodePort       10.110.170.58   <none>        80:32000/TCP   9m18s

$ curl localhost:31018
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

# Ćwiczenie 2
Utwórz dwa Pody z aplikacją helloapp, które mają po jednym wspólnym Label, oraz posiadają oprócz tego inne Label (poniżej przykład).

```
# Pod 1
labels:
  app: helloapp
  ver: v1

# Pod 2
labels:
  app: helloapp
  ver: v1
```
Do tak utworzonych Podów podepnij serwis i sprawdź jak się zachowuje.

PODY:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: helloapp-1
  labels:
    app: helloapp
    ver: v1
spec:
  containers:
  - name: helloapp-1
    image: poznajkubernetes/helloapp:svc
    ports:
    - containerPort: 8080
      name: http
      protocol: TCP
---
apiVersion: v1
kind: Pod
metadata:
  name: helloapp2
  labels:
    app: helloapp
    ver: v2
spec:
  containers:
  - name: helloapp-2
    image: poznajkubernetes/helloapp:svc
    ports:
    - containerPort: 8080
      name: http
      protocol: TCP
```

SWERWIS:
```yaml
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
$ k apply -f cw1-2-helloapp.yaml
pod/helloapp-1 created
pod/helloapp2 created

$ k get pods -o wide
NAME         READY   STATUS    RESTARTS   AGE   IP           NODE             NOMINATED NODE   READINESS GATES
helloapp-1   1/1     Running   0          18s   10.1.0.236   docker-desktop   <none>           <none>
helloapp2    1/1     Running   0          18s   10.1.0.237   docker-desktop   <none>           <none>
nginx        1/1     Running   0          45m   10.1.0.235   docker-desktop   <none>           <none>
tools        1/1     Running   1          85m   10.1.0.233   docker-desktop   <none>           <none>

$ k apply -f cw1-2-helloapp-svc.yaml 
service/helloapp-port created

$ k get svc
NAME                   TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
helloapp-port          ClusterIP      10.106.154.145   <none>        80/TCP         23s
kubernetes             ClusterIP      10.96.0.1        <none>        443/TCP        28d
loadbalancer-nginx     LoadBalancer   10.106.7.71      localhost     80:30069/TCP   18m
nodeport-nginx         NodePort       10.110.28.251    <none>        80:31018/TCP   37m
nodeport-nginx-named   NodePort       10.110.170.58    <none>        80:32000/TCP   28m

$ k exec -it tools sh
/ # curl 10.106.154.145
Cześć, 🚢 =>  helloapp2
/ # curl 10.106.154.145
Cześć, 🚢 =>  helloapp2
/ # curl 10.106.154.145
Cześć, 🚢 =>  helloapp-1
/ # curl 10.106.154.145
Cześć, 🚢 =>  helloapp-1
/ # curl 10.106.154.145
Cześć, 🚢 =>  helloapp-1
/ # curl 10.106.154.145
Cześć, 🚢 =>  helloapp2
/ # 
```
