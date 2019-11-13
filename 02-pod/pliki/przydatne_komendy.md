# utworzenie pod z pliku
kubectl create -f pod.yaml

# utworzenie pod z pliku z zapamiętaną konfiguracją
kubectl create -f pod.yaml --save-config=true

# utworzenie lub update pod na podstawie pliku
kubectl apply -f pod.yaml

# pobranie podów
kubectl get pods

# pobranie definicji pod
kubectl get pod NAME -o yaml|json

# usunięcie pod na podstawie pliku z definicją
kubectl delete -f pod.yaml

# usunięcie pod o danej nazwie
kubectl delete pod NAME

# utworzenie definicji Pod i zapisanie go w pliku pod.yaml
kubectl run NAME --image=IMAGE --generator=run-pod/v1  --dry-run -o yaml > pod.yaml

