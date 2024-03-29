
### Na podstawie demo i materiałów z poprzedniej lekcji wykorzystaj Vault do przekazania konfiguracji.
W swoim Pod wygeneruj plik konfiguracyjny typu json lub xml ze secretami z Vault.

instalacja vaulta:

```
$ k apply -f vault.yaml
pod/vault created
```
```
$ kubectl logs vault | grep Token
Root Token: s.TTe9VRbxlFGeomWcq65zKSCQ
```
```
$ export VAULT_TOKEN=s.TTe9VRbxlFGeomWcq65zKSCQ
```
```
$ export VAULT_ADDR=http://127.0.0.1:8200
```

```
$ k port-forward vault 8200 &

$ Forwarding from 127.0.0.1:8200 -> 8200
Forwarding from [::1]:8200 -> 8200
```

Konfiguracja:

```bash
#!/bin/bash

# Create the 'vault-auth' service account
kubectl apply --filename vault-auth-service-account.yml

# Create a policy named myapp-kv-ro
vault policy write myapp-kv-ro myapp-kv-ro.hcl

# Create test data in the `secret/myapp` path.
vault kv put secret/myapp/config username='appuser' password='suP3rsec(et!' ttl='30s'

# Set VAULT_SA_NAME to the service account you created earlier
export VAULT_SA_NAME=$(kubectl get sa vault-auth -o jsonpath="{.secrets[*]['name']}")

# Set SA_JWT_TOKEN value to the service account JWT used to access the TokenReview API
export SA_JWT_TOKEN=$(kubectl get secret $VAULT_SA_NAME -o jsonpath="{.data.token}" | base64 --decode; echo)

# Set SA_CA_CRT to the PEM encoded CA cert used to talk to Kubernetes API
export SA_CA_CRT=$(kubectl get secret $VAULT_SA_NAME -o jsonpath="{.data['ca\.crt']}" | base64 --decode; echo)

# Set K8S_HOST to minikube IP address
export K8S_HOST=kubernetes.docker.internal

# Enable the Kubernetes auth method at the default path ("auth/kubernetes")
vault auth enable kubernetes

# Tell Vault how to communicate with the Kubernetes (Minikube) cluster
vault write auth/kubernetes/config token_reviewer_jwt="$SA_JWT_TOKEN" kubernetes_host="https://$K8S_HOST:6443" kubernetes_ca_cert="$SA_CA_CRT"

# Create a role named, 'example' to map Kubernetes Service Account to
#  Vault policies and default token TTL

vault write auth/kubernetes/role/example bound_service_account_names=demo-pod bound_service_account_namespaces=default policies=myapp-kv-ro ttl=24h
```

```
$ kubectl create configmap example-vault-agent-config --from-file=./configs-k8s/
configmap/example-vault-agent-config created
```
```
kubectl apply -f example-k8s-spec.yaml
```