# k8s-chaos

## Configure cluster
### Install Chaos-Mesh
```BASH
helm repo add chaos-mesh https://charts.chaos-mesh.org
helm repo update
helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --create-namespace --set dashboard.create=true
helm upgrade chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --set dashboard.securityMode=false
```

## Configure app
### Install app for experiment
```BASH
kubectl create ns chaos-app
kubectl annotate ns chaos-app chaos-mesh.org/inject=enabled
kubectl -n chaos-app create deploy httpd --image=httpd --replicas=2
```

### Run experiment "pod-kill"
```BASH
kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/pod-kill.yaml
watch kubectl -n chaos-app get pod
kubectl delete -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/pod-kill.yaml
```
Until now Chaos Mesh didn't support updating a chaos. You can choose to create a new chaos with a different name instead.

### Run cyclic experiment "pod-kill"
```BASH
kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/cyclic-pod-kill.yaml
watch kubectl -n chaos-app get pod
kubectl describe podchaos schedule-pod-kill-example -n chaos-testing
kubectl delete -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/cyclic-pod-kill.yaml
```

### Clear
```BASH
helm uninstall chaos-mesh --namespace=chaos-testing
kubectl -n chaos-app delete all -l app=httpd
kubectl delete ns chaos-app
kubectl delete ns chaos-app
```

###Reference links


* [workflow](https://chaos-mesh.org/docs/2.3.3/create-chaos-mesh-workflow/)
* [github-actions](https://chaos-mesh.org/docs/2.3.3/integrate-chaos-mesh-into-github-actions/)
