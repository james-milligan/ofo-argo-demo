# Openfeature demo app deployment ft. argo

install cert manager
```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.9.1/cert-manager.yaml
kubectl wait --for=condition=Available=True deploy --all -n 'cert-manager'
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

deploy the openfeature-operator application
```
argocd app create openfeature-operator --repo https://github.com/open-feature/open-feature-operator.git --path releases/download/v0.2.2 --dest-server https://kubernetes.default.svc --dest-namespace open-feature-operator-system

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
