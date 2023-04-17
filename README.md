# k8s-chaos

## Configure cluster
### Install Chaos-Mesh
```BASH
helm repo add chaos-mesh https://charts.chaos-mesh.org
helm repo update
helm install chaos-mesh chaos-mesh/chaos-mesh --set chaosDaemon.env.DOCKER_API_VERSION=1.40 --namespace=chaos-testing --create-namespace --set dashboard.create=true --set dashboard.securityMode=false
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
kubectl apply -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/pod-faults/pod-kill.yaml
watch kubectl -n chaos-app get pod
kubectl delete -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/pod-faults/pod-kill.yaml
```
Until now Chaos Mesh didn't support updating a chaos. You can choose to create a new chaos with a different name instead.

### Run cyclic experiment "pod-kill"
```BASH
kubectl apply -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/pod-faults/cyclic-pod-kill.yaml
watch kubectl -n chaos-app get pod
kubectl describe podchaos schedule-pod-kill-example -n chaos-testing
kubectl delete -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/pod-faults/cyclic-pod-kill.yaml
```

### Run experiment "network-faults"
```BASH
kubectl apply -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/network-faults/deployment_app.yaml
kubectl -n chaos-app run mycurlpod --image=curlimages/curl -i --tty -- sh
curl -X GET http://chaos-app-backend:5000
```
```BASH
for i in `seq 10000`; do curl -X GET http://chaos-app-backend:5000;\n; sleep 0.1; done
kubectl apply -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/network-faults/network-faults-delay.yaml
```

### Run experiment for web app
```BASH
sh ./app.sh
kubectl patch svc web-show -n chaos-app -p '{"spec": {"type": "NodePort"}}'
```
```BASH
kubectl apply -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/web-app/cyclic-web-kill-pod.yaml
```
```BASH
kubectl apply -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/web-app/cyclic-web-failure-pod.yaml
```
```BASH
kubectl apply -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/web-app/cyclic-web-network-delay.yaml
```
```BASH
kubectl apply -f https://raw.githubusercontent.com/geksogen/k8s-chaos/master/experiments/web-app/cyclic-web-network-bandwidth.yaml
```

### Run workflow for web app
kubectl apply -f 

### Clear
```BASH
helm uninstall chaos-mesh --namespace=chaos-testing
kubectl -n chaos-app delete all -l app=httpd
kubectl -n chaos-app delete all -l app=chaos-app-backend
kubectl -n chaos-app delete all -l app=web-show
kubectl delete ns chaos-app
kubectl delete ns chaos-testing
```

###Reference links

* [workflow](https://chaos-mesh.org/docs/2.3.3/create-chaos-mesh-workflow/)
* [github-actions](https://chaos-mesh.org/docs/2.3.3/integrate-chaos-mesh-into-github-actions/)
* [examples-experement](https://github.com/chaos-mesh/chaos-mesh/tree/master/examples)