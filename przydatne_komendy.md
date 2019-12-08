#
# POD
#

# utworzenie pod z pliku
kubectl create -f pod.yaml

# utworzenie pod z pliku z zapamiętaną konfiguracją
kubectl create -f pod.yaml --save-config=true

# utworzenie lub update pod na podstawie pliku
kubectl apply -f pod.yaml

# pobranie podów
kubectl get pods

# nasłuchuj na zmiany w podach
kubectl get pods -w

# pobranie definicji pod
kubectl get pod NAME -o yaml|json

# pobierz inforamcje na temat poda
kubectl describe pod nazwa_poda

# usunięcie pod na podstawie pliku z definicją
kubectl delete -f pod.yaml

# usunięcie pod o danej nazwie
kubectl delete pod NAME

# utworzenie definicji Pod i zapisanie go w pliku pod.yaml
kubectl run NAME --image=IMAGE --generator=run-pod/v1  --dry-run -o yaml > pod.yaml

# konsola kontenera 
kubectl exec -it containerName bash

# przekierowanie portu hostPort:containerPort
kubectl port-forward web 8080:80

# pobierz logi danego poda (jest parenaście opcji w tym --follow, --tail itp)
kubectl logs nazwa_poda

# pobierz logi danego kontenera z poda
kubectl logs nazwa_poda -c nazwa_kontenera

# pobierz logi danego kontenera z poda
kubectl logs nazwa_poda -c nazwa_kontenera

# uruchom pod
kubectl run nazwa_poda --image=obraz --restart=Never

# uruchom pod tymczasowy na czas trwania sesji
kubectl run nazwa_poda --iamge=obraz --restart=Never --rm -it -- /bin/sh

# uruchom komendę w pod
kubectl exec nazwa_poda -- komenda

# uruchom komendę w danym kontenerze poda
kubectl exec nazwa_poda -c nazwa_kontenera -- komenda

# uruchom shell w kontenerze poda
kubectl exec nazwa_poda -c nazwa_kontenera -it -- /bin/sh

# przekieruj porty z komputera do poda
kubectl port-forward nazwa_poda PORT_LOKALNY:PORT_W_POD

# uruchom proxy do api
kubectl proxy

# 
# CONFIGMAP
# 


# pobranie ConfigMap w domyślnej przestrzeni nazw
kubectl get cm

# opis ConfigMapy z informacją o zawartości
kubectl describe cm

# usunięcie ConfigMapy
kubectl delete cm

# stworzenie ConfigMapy
kubectl create cm NAME --from-literal=key=value --from-file=file.ext

# przekierowanie portów lokalnych na te z kontenera
kubectl port-forward

# uruchomienie polecenia w kontenerze na Pod
kubectl exec POD -- printenv

#
# SECRESTS
#

# utworzenie secter z lini poleceń
kubectl create secret generic secret-literal --from-literal=user=poznaj --from-literal=pass=kubernetets

# utworzenie secret z pliku
kubectl create secret generic secret-file --from-file=secrets.txt

# pobranie secret
kubectl get secrets secret-literal -o yaml

# decode base 64
echo cG96bmFq | base64 -d

# utworzenie secret do z docker config
kubectl create secret generic crcreg --from-file=.dockerconfigjson=/home/user/.docker/config.json --type=kubernetes.io/dockerconfigjson

#
# GRUPOWANIE - WYBIERANIE
#

# dodaj nową etytkietę
kubectl label pods NAME labelName=labelValue

# dodaj nową etykietę do wszystkich Podów
kubectl label pods --all labelName=labelValue

# zaktualizuj etykietę
kubectl label pods NAME --overwrite labelName=newValue

# usuń etykietę
kubectl label pods NAME labelName-

# pokaż wszystkie etykiety zdefiniowane w zasobie
kubectl get RESOURCE --show-labels

# pokaż etykietę labelName jako nową kolumnę w liście Podów
# zamiast -L można stosować --label-columns
kubectl get pods -L labelName

# pokaż tylko i wyłącznie Pody które mają etykietę env
# zamiast --selector można stosowoać -l (małe l)
kubectl get pods --selector env

# pokaż tylko i wyłącznie Pody które nie mają etykiety env
kubectl get pods --selector '!env'

# pokaż Pody które mają env=test i app=ui
kubectl get pods -l env=test,app=ui

# pokaż Pody które mają env z przedziału test i stg
kubectl get pods -l 'env in (test, stg)'

# pokaż Pody które nie mają env z przedziału test i stg
kubectl get pods -l 'env notin (test, stg)'

# pokaż jedynie Pody które nie są wstanie Running
kubectl get pods --field-selector status.phase!=Running

# posrotuj Pody po fazie
kubectl get pods --sort-by=.status.phase

# posrotuj Pody dacie utworzenia
kubectl get pods --sort-by=.metadata.creationTimestamp

# wyświetl jedynie nazwę poda
kubectl get pods -o jsonpath='{.items[*].metadata.name}'

# wyświetl nazwę poda w nowej linicje
# uwaga na windows zamiast jsonpath='' to jsonpath="" wiec "\n" to \"\n\"
kubectl get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\n"}{end}'

#
#   Serwisy i Service Discovery
#

# Pod do sprawdzania nslookup
kubectl run -it --rm tools --generator=run-pod/v1 --image=giantswarm/tiny-tools

# pobierz wszystkie Services
kubectl get svc
kubectl get services
 
# opis services
kubectl describe svc
 
# pobierz wszystkie Endpointy
kubectl get endpoints
 
# Pod do sprawdzania nslookup
kubectl run -it --rm tools --generator=run-pod/v1 --image=giantswarm/tiny-tools

# Utworzenie namespaces
kubectl create ns <nazwa namespaces>

# pobierz wszystkie Services z namespaces
kubectl get svc -n <nazwa namespaces>
