---
layout: article
title: kubernetes cluster dashboard deploy
image: /img/k8s.png
tags: k8s kubernetes installation ubuntu kubeadm monitoring
category: k8s
---

```
wget https://raw.githubusercontent.com/helm/charts/master/stable/kubernetes-dashboard/values.yaml
helm install stable/kubernetes-dashboard --name dashboard -f values.yaml
```

login token :
found replicaset-controller-token by
```
kubectl -n kube-system get secret
kubectl -n kube-system describe secret replicaset-controller-token-XXXXX
```
