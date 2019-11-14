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
