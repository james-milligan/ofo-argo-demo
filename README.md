```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
kubectl wait --for=condition=Available=True deploy --all -n 'argocd' 
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
argocd login localhost:8080
```

```
argocd app create openfeature --repo https://github.com/james-milligan/ofo-argo-demo.git --path deployment --dest-server https://kubernetes.default.svc --dest-namespace default
```

