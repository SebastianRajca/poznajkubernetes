# Stwórz Pod na bazie obrazu z modułu 1
```yaml
apiVersion: v1
kind: Pod
metadata:
 name: kuard
spec:
 containers:
 - image: poznajkubernetes/kuard
   name: kuard
```
```
k apply -f cw3pod_internals.yaml 
```
## Zobacz w jakim stanie on się znajduje

```
$ k get pod -w
NAME    READY   STATUS    RESTARTS   AGE
kuard   0/1     Pending   0          0s
kuard   0/1     Pending   0          0s
kuard   0/1     ContainerCreating   0          0s
kuard   1/1     Running             0          5s
```
### Podejrzyj jego logi

```
$ kubectl logs kuard 
2019/11/15 18:08:03 Starting pkad version: blue
2019/11/15 18:08:03 **********************************************************************
2019/11/15 18:08:03 * WARNING: This server may expose sensitive
2019/11/15 18:08:03 * and secret information. Be careful.
2019/11/15 18:08:03 **********************************************************************
2019/11/15 18:08:03 Config: 
{
  "address": ":8080",
  "debug": false,
  "debug-sitedata-dir": "./sitedata",
  "keygen": {
    "enable": false,
    "exit-code": 0,
    "exit-on-complete": false,
    "memq-queue": "",
    "memq-server": "",
    "num-to-gen": 0,
    "time-to-run": 0
  },
  "liveness": {
    "fail-next": 0
  },
  "readiness": {
    "fail-next": 0
  },
  "tls-address": ":8443",
  "tls-dir": "/tls"
}
2019/11/15 18:08:03 Could not find certificates to serve TLS
2019/11/15 18:08:03 Serving on HTTP on :8080
```
## Wykonaj w koneterze wylistowanie katalogów

```
$ kubectl exec kuard -it -- /bin/sh
~ $ ls
bin    etc    lib    mnt    pkad   root   sbin   sys    usr
dev    home   media  opt    proc   run    srv    tmp    var
~ $ 
```
## Odpytaj się http://localhost:PORT w koneterze

```
$ kubectl exec kuard -- wget -qO- http://localhost:8080
<!doctype html>

<html lang="en">
<head>
  <meta charset="utf-8">

  <title>Poznaj Kubernetes Demo</title>

  <link rel="stylesheet" href="/static/css/bootstrap.min.css">
  <link rel="stylesheet" href="/static/css/styles.css">

  <script>
var pageContext = {"urlBase":"","hostname":"kuard","addrs":["10.1.0.22
"],"version":"blue","versionColor":"hsl(21,100%,50%)","requestDump":"G
ET / HTTP/1.1\r\nHost: localhost:8080\r\nConnection: close\r\nConnecti
on: close\r\nUser-Agent: Wget","requestProto":"HTTP/1.1","requestAddr"
:"127.0.0.1:52342"}
  </script>
</head>


<svg style="position: absolute; width: 0; height: 0; overflow: hidden;
" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http:/
/www.w3.org/1999/xlink">
<defs>
<symbol id="icon-power" viewBox="0 0 32 32">
<title>power</title>
<path class="path1" d="M12 0l-12 16h12l-8 16 28-20h-16l12-12z"></path>
</symbol>
<symbol id="icon-notification" viewBox="0 0 32 32">
<title>notification</title>
<path class="path1" d="M16 3c-3.472 0-6.737 1.352-9.192 3.808s-3.808 5
.72-3.808 9.192c0 3.472 1.352 6.737 3.808 9.192s5.72 3.808 9.192 3.808c3.472 0 6.737-1.352 9.192-3.808s3.808-5.72 3.808-9.192c0-3.472-1.352-6.737-3.808-9.192s-5.72-3.808-9.192-3.808zM16 0v0c8.837 0 16 7.163 16 16s-7.163 16-16 16c-8.837 0-16-7.163-16-16s7.163-16 16-16zM14 22h4v4h-4zM14 6h4v12h-4z"></path>
</symbol>
</defs>
</svg>

<body>
  <div id="root"></div>
  <script src="/built/bundle.js" type="text/javascript"></script>
  <script src="/static/js/jquery-3.3.1.slim.min.js" type="text/javascript"></script>
  <script src="/static/js/popper.min.js" type="text/javascript"></script>
  <script src="/static/js/bootstrap.min.js" type="text/javascript"></script>
</body>
</html>
[user@SiMKomputer:pliki (⎈|docker-desktop:default)]
$ 
```
## Zrób to samo z powołanego osobno poda (kubectl run)

```
$ kubectl run cw3 --image=busybox --restart=Never --rm -it -- /bin/sh
If you don't see a command prompt, try pressing enter.
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # hostname
cw3
/ # wget -qO- http://10.1.0.22:8080
<!doctype html>

<html lang="en">
<head>
  <meta charset="utf-8">

  <title>Poznaj Kubernetes Demo</title>

  <link rel="stylesheet" href="/static/css/bootstrap.min.css">
  <link rel="stylesheet" href="/static/css/styles.css">

  <script>
var pageContext = {"urlBase":"","hostname":"kuard","addrs":["10.1.0.22"],"version":"blue","versionColor":"hsl(21,100%,50%)","requestDump":"GET / HTTP/1.1\r\nHost: 10.1.0.22:8080\r\nConnection: close\r\nConnection: close\r\nUser-Agent: Wget","requestProto":"HTTP/1.1","requestAddr":"10.1.0.23:56224"}
  </script>
</head>


<svg style="position: absolute; width: 0; height: 0; overflow: hidden;" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<defs>
<symbol id="icon-power" viewBox="0 0 32 32">
<title>power</title>
<path class="path1" d="M12 0l-12 16h12l-8 16 28-20h-16l12-12z"></path>
</symbol>
<symbol id="icon-notification" viewBox="0 0 32 32">
<title>notification</title>
<path class="path1" d="M16 3c-3.472 0-6.737 1.352-9.192 3.808s-3.808 5.72-3.808 9.192c0 3.472 1.352 6.737 3.808 9.192s5.72 3.808 9.192 3.808c3.472 0 6.737-1.352 9.192-3.808s3.808-5.72 3.808-9.192c0-3.472-1.352-6.737-3.808-9.192s-5.72-3.808-9.192-3.808zM16 0v0c8.837 0 16 7.163 16 16s-7.163 16-16 16c-8.837 0-16-7.163-16-16s7.163-16 16-16zM14 22h4v4h-4zM14 6h4v12h-4z"></path>
</symbol>
</defs>
</svg>

<body>
  <div id="root"></div>
  <script src="/built/bundle.js" type="text/javascript"></script>
  <script src="/static/js/jquery-3.3.1.slim.min.js" type="text/javascript"></script>
  <script src="/static/js/popper.min.js" type="text/javascript"></script>
  <script src="/static/js/bootstrap.min.js" type="text/javascript"></script>
</body>
</html>
/ # 
```
## Dostań się do poda za pomocą przekierowania portów

```
$ kubectl port-forward kuard 8080:8080
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
Handling connection for 8080
Handling connection for 8080
Handling connection for 8080
Handling connection for 8080
Handling connection for 8080
Handling connection for 8080
```

### Dostań się do poda za pomocą API Server

```
kubectl proxy
Starting to serve on 127.0.0.1:8001
```
```
http://localhost:8001/api/v1/namespaces/default/pods/kuard
```

```json
{
  "kind": "Pod",
  "apiVersion": "v1",
  "metadata": {
    "name": "kuard",
    "namespace": "default",
    "selfLink": "/api/v1/namespaces/default/pods/kuard",
    "uid": "dae7de4e-07d2-11ea-9561-00155d0b6109",
    "resourceVersion": "26458",
    "creationTimestamp": "2019-11-15T18:07:58Z",
    "annotations": {
      "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"Pod\",\"metadata\":{\"annotations\":{},\"name\":\"kuard\",\"namespace\":\"default\"},\"spec\":{\"containers\":[{\"image\":\"poznajkubernetes/kuard\",\"name\":\"kuard\"}]}}\n"
    }
  },
  "spec": {
    "volumes": [
      {
        "name": "default-token-95bjm",
        "secret": {
          "secretName": "default-token-95bjm",
          "defaultMode": 420
        }
      }
    ],
    "containers": [
      {
        "name": "kuard",
        "image": "poznajkubernetes/kuard",
        "resources": {
          
        },
        "volumeMounts": [
          {
            "name": "default-token-95bjm",
            "readOnly": true,
            "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount"
          }
        ],
        "terminationMessagePath": "/dev/termination-log",
        "terminationMessagePolicy": "File",
        "imagePullPolicy": "Always"
      }
    ],
    "restartPolicy": "Always",
    "terminationGracePeriodSeconds": 30,
    "dnsPolicy": "ClusterFirst",
    "serviceAccountName": "default",
    "serviceAccount": "default",
    "nodeName": "docker-desktop",
    "securityContext": {
      
    },
    "schedulerName": "default-scheduler",
    "tolerations": [
      {
        "key": "node.kubernetes.io/not-ready",
        "operator": "Exists",
        "effect": "NoExecute",
        "tolerationSeconds": 300
      },
      {
        "key": "node.kubernetes.io/unreachable",
        "operator": "Exists",
        "effect": "NoExecute",
        "tolerationSeconds": 300
      }
    ],
    "priority": 0,
    "enableServiceLinks": true
  },
  "status": {
    "phase": "Running",
    "conditions": [
      {
        "type": "Initialized",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2019-11-15T18:07:58Z"
      },
      {
        "type": "Ready",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2019-11-15T18:08:03Z"
      },
      {
        "type": "ContainersReady",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2019-11-15T18:08:03Z"
      },
      {
        "type": "PodScheduled",
        "status": "True",
        "lastProbeTime": null,
        "lastTransitionTime": "2019-11-15T18:07:58Z"
      }
    ],
    "hostIP": "192.168.65.3",
    "podIP": "10.1.0.22",
    "startTime": "2019-11-15T18:07:58Z",
    "containerStatuses": [
      {
        "name": "kuard",
        "state": {
          "running": {
            "startedAt": "2019-11-15T18:08:03Z"
          }
        },
        "lastState": {
          
        },
        "ready": true,
        "restartCount": 0,
        "image": "poznajkubernetes/kuard:latest",
        "imageID": "docker-pullable://poznajkubernetes/kuard@sha256:230ff75987cf38d9d90ac1684d445f2d02f3edfa45865a0de35bc94f4a38c83b",
        "containerID": "docker://8b485d84773b22ddca345e78a22d7e86f33e9e96febeeb740ef3cad09349544b"
      }
    ],
    "qosClass": "BestEffort"
  }
}
```

# Stwórz Pod zawierający dwa kontenery – busybox i poznajkubernetes/helloapp:multi

```
apiVersion: v1
kind: Pod
metadata:
 name: helloapp
spec:
 containers:
 - image: busybox
   name: helloapp
 - image: poznajkubernetes/helloapp:multi
   name: kuard
```
```
$ k apply -f cw3dwa_kontenery.yaml 
```
## Zweryfikuj, że Pod działa poprawnie

jeden z kontenerów dostaje status CrashLoopBackOff

```
$ k get pod -w
NAME       READY   STATUS    RESTARTS   AGE
helloapp   0/2     Pending   0          0s
helloapp   0/2     Pending   0          0s
helloapp   0/2     ContainerCreating   0          1s
helloapp   1/2     Running             0          7s
helloapp   1/2     Running             1          17s
helloapp   1/2     CrashLoopBackOff    1          18s
```

## Jak nie działa, dowiedz się dlaczego

Kontener busybox nie robi nic i kończy swoja prace ExitCode:0
Kubernetes próbuje ko ponownie uruchomić ale wpada w pętle.

```
$ k describe pod helloapp
Name:               helloapp
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               docker-desktop/192.168.65.3
Start Time:         Fri, 15 Nov 2019 20:09:49 +0100
Labels:             <none>
Annotations:        kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"name":"helloapp","namespace":"default"},"spec":{"containers":[{"image":"pozn...
Status:             Running
IP:                 10.1.0.26
Containers:
  helloapp:
    Container ID:   docker://dca1be3ef652eb2fcb5378aff90ca3b12774286c02c6c797d15bdbc58acf5402
    Image:          poznajkubernetes/helloapp:multi
    Image ID:       docker-pullable://poznajkubernetes/helloapp@sha256:6bae4ef606a02436aa94e5eb9dfb62e943f6a152cfabbafb7c61508b1c48e222
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Fri, 15 Nov 2019 20:09:50 +0100
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-95bjm (ro)
  busybox:
    Container ID:   docker://862adcf8a6ffd6e3733191fbbb0b2e61956d1a45472031d805967e81b4f2a03b
    Image:          busybox
    Image ID:       docker-pullable://busybox@sha256:1303dbf110c57f3edf68d9f5a16c082ec06c4cf7604831669faf2c712260b5a0
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Fri, 15 Nov 2019 20:10:29 +0100
      Finished:     Fri, 15 Nov 2019 20:10:29 +0100
    Ready:          False
    Restart Count:  2
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-95bjm (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  default-token-95bjm:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-95bjm
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age                From                     Message
  ----     ------     ----               ----                     -------
  Normal   Scheduled  71s                default-scheduler        Successfully assigned default/helloapp to docker-desktop
  Normal   Pulled     70s                kubelet, docker-desktop  Container image "poznajkubernetes/helloapp:multi" already present on machine
  Normal   Created    69s                kubelet, docker-desktop  Created container helloapp
  Normal   Started    69s                kubelet, docker-desktop  Started container helloapp
  Normal   Pulled     30s (x3 over 65s)  kubelet, docker-desktop  Successfully pulled image "busybox"
  Normal   Created    30s (x3 over 65s)  kubelet, docker-desktop  Created container busybox
  Normal   Started    30s (x3 over 65s)  kubelet, docker-desktop  Started container busybox
  Warning  BackOff    15s (x4 over 54s)  kubelet, docker-desktop  Back-off restarting failed container
  Normal   Pulling    4s (x4 over 69s)   kubelet, docker-desktop  Pulling image "busybox"
```

## Zastanów się nad rozwiązaniem problemu jeżeli istnieje – co można by było zrobić i jak

  1. `restartPolicy: OnFailure` albo  `restartPolicy: Never`
  ale z tego co rozumiem to ustawiam dla cąłego Poda więc i dla działającego kontenera.
  2. Monza też dac cos do roboty kontenerowi busybox.
  3. Ewentualnie odpalić jakieś jednorazowe zadanie jako initContainer