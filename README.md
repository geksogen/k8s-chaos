# k8s-chaos

### Configure cluster
## Install Chaos-Mesh
```BASH
helm repo add chaos-mesh https://charts.chaos-mesh.org
helm repo update
helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --create-namespace --set dashboard.create=true
```

### Configure app
## Install app for experiment
```BASH
kubectl create ns chaos-app
kubectl -n chaos-app create deploy httpd --image=httpd --replicas=2
```

## Run experiment "pod-kill"
```BASH
kubectl create ns chaos-app
kubectl -n chaos-app create -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/pod-kill.yaml
```



## Clear
```BASH
helm uninstall chaos-mesh
```