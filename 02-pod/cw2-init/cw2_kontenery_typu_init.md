Jako initContainer pobrałem ubuntu:latest.

Nie tworzyłem własnego obrazu z zainstalowanym gitem bo chciałem sprawdzić czy da się go doinstalować w locie w trakcie inicjalizacji poda.

Wiem że raczej na produkcji tak się nie robi bo za długo to trwa, ale test udany i działa.


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web
  labels:
    name: web
spec:
  containers:
  - name: web
    image: nginx
    ports:
      - containerPort: 80
    volumeMounts:
    - name: workdir
      mountPath: /usr/share/nginx/html
  initContainers:
  - name: install
    image: ubuntu
    command: ["/bin/sh", "-c"]
    args:
      - apt-get update;
        apt-get install -y git;
        git clone "https://github.com/PoznajKubernetes/poznajkubernetes.github.io" /work-dir;
    volumeMounts:
    - name: workdir
      mountPath: "/work-dir"
  volumes:
  - name: workdir
    emptyDir: {}
```  