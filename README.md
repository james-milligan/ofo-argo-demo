# Openfeature demo app deployment ft. argo

install the openfeature operator
```
git clone https://github.com/open-feature/open-feature-operator
cd open-feature-operator && IMG=ghcr.io/open-feature/open-feature-operator:main make deploy-operator
```

deploy argocd to cluster
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
kubectl wait --for=condition=Available=True deploy --all -n 'argocd' 
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
authenticate argocd cli
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
argocd login localhost:8080
```
deploy the openfeature app to argocd
```
argocd app create openfeature --repo https://github.com/james-milligan/ofo-argo-demo.git --path deployment --dest-server https://kubernetes.default.svc --dest-namespace default
```
sync the openfeature app
```
argocd app sync openfeature
```

port forward for the playground app
```
kubectl port-forward svc/open-feature-demo-service 30000:30000
```
