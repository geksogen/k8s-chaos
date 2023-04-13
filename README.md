# k8s-chaos

### Configure cluster
## Install Chaos-Mesh
```BASH
helm repo add chaos-mesh https://charts.chaos-mesh.org
helm repo update
helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --create-namespace --set dashboard.create=true
helm upgrade chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --set dashboard.securityMode=false
```

### Configure app
## Install app for experiment
```BASH
kubectl create ns chaos-app
kubectl annotate ns chaos-app chaos-mesh.org/inject=enabled
kubectl -n chaos-app create deploy httpd --image=httpd --replicas=2
```

## Run experiment "pod-kill"
```BASH
kubectl -n chaos-app create -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/pod-kill.yaml
kubectl delete -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/pod-kill.yaml
```

Ref #2227. Until now Chaos Mesh didn't support updating a chaos. You can choose to create a new chaos with a different name instead.

## Clear
```BASH
helm uninstall chaos-mesh
```