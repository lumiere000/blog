---
layout: article
title: installation and config of prometheus in k8s
cover: /img/prometheus.webp
tags: installation config tools monitoring k8s
category: k8s
---

### download

```
helm repo add coreos https://s3-eu-west-1.amazonaws.com/coreos-charts/stable/
helm fetch coreos/prometheus-operator
helm fetch coreos/kube-prometheus
```

```
tar -zxf kube-prometheus-0.0.105.tgz
tar -zxf prometheus-operator-0.0.29.tgz
```

```
vi prometheus-operator/values.yaml
```

### change following :

```
prometheusConfigReloader:
  repository: quay.io/coreos/prometheus-config-reloader
  tag: v0.22.2
```

```
image:
  repository: quay.io/coreos/prometheus-operator
  tag: v0.22.2
  pullPolicy: IfNotPresent
```

```
vi kube-prometheus/values.yaml
```

### Select Deployed DNS Solution

```
deployCoreDNS: true
deployKubeDNS: false
deployKubeEtcd: true
```

```
cd /home/cluster_conf/prometheus-operator
helm install --name prometheus-operator --namespace monitoring -f values.yaml .
```

```
cd /home/cluster_conf/kube-prometheus
helm install --name kube-prometheus --namespace monitoring -f values.yaml .
```

```
kubectl edit svc kube-prometheus -n monitoring

  externalIPs:
  - 192.168.1.80
```

```  
kubectl edit svc kube-prometheus-grafana -n monitoring

  externalIPs:
  - 192.168.1.80

  ports:
  - name: http
    port: 3000
    protocol: TCP
    targetPort: 3000
```

```
kubectl edit svc kube-prometheus-exporter-kube-scheduler -n kube-system
  selector:
    component: kube-scheduler
```

```
kubectl edit svc kube-prometheus-exporter-kube-controller-manager -n kube-system
  selector:
    component: kube-controller-manager
```

```  
find your 3 kube-controller-manager by :
kubectl get pod -n kube-system
```

```
kubectl edit pod kube-controller-manager-k8smaster01 -n kube-system
  labels:
    component: kube-controller-manager
    tier: control-plane
```

```
kubectl -n kube-system set selector service kube-prometheus-exporter-kube-controller-manager  component=kube-controller-manager
kubectl -n kube-system set selector service kube-prometheus-exporter-kube-scheduler  component=kube-scheduler
kubectl get po -l component -n kube-system --show-labels
```

### alertmanager for pvc

```
cd /home/cluster_conf/kube-prometheus/charts/exporter-kubernetes/templates
vi kubernetes.rules.yaml

add before {{ end }}

  - alert: K8sPVCDiskFull
    labels:
      severity: warning
    annotations:
      description: Kubernetes PVC space over 90%
      summary: Kubernetes PVC DiskFull
    expr: 1 / kubelet_volume_stats_capacity_bytes * kubelet_volume_stats_used_bytes *100 > 90
```


### config on each node :

```
vi /etc/kubernetes/manifests/kube-controller-manager.yaml
    - --address=0.0.0.0
vi /etc/kubernetes/manifests/kube-scheduler.yaml
	- --address=0.0.0.0
```

### restart nodes kubelet one by one :

```
systemctl restart kubelet
```
