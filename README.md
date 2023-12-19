# brief10

Installation de Prometheus et Grafana sur un cluster AKS  
Le cluster utilisé est le même que les briefs précédents, les manifestes utilisés étant ceux du brief 6  

## Installation de Prometheus

Via Helm, au préalable un namespace "monitoring" a été créé :

Récupération du repo prometheus :  

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

puis installation de prometheus :  
```
helm install prometheus prometheus-community/prometheus
```

Vérification des pods :

```
kubectl get pods -n monitoring
```

## Installation de Grafana  

Toujours en passant par helm

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install grafana grafana/grafana --namespace monitoring
```

## Port-forward et connexion  

Pour accéder aux interfaces de prometheus et grafana : portforward

```
kubectl port-forward svc/prometheus-grafana -n monitoring 4000:80
kubectl port-forward svc/prometheus-kube-prometheus-prometheus -n monitoring 4001:9090
```  
Accessibles depuis 127.0.0.1:4000 pour Grafana  
127.0.0.1:4001 pour prometheus  

Depuis l'interface de Grafana : mise en place d'alertes de monitoring sur RAM et CPU :  

Mémoire en pourcentage d'utilisation :  
sum(container_memory_working_set_bytes) / sum(kube_pod_container_resource_limits) x 100  

Charge CPU en pourcentage :  
sum(rate(container_cpu_usage_seconds_total)) / sum(kube_pod_container_resource_limits) x 100  
