---
id: chaoscenter-cluster-scope-installation
title: ChaosCenter Cluster Scope Installation
sidebar_label: Cluster Scope
---

---

## Prerequisites

Before deploying LitmusChaos, make sure the following items are there

- Kubernetes 1.17 or later

- A Persistent volume of 20GB

  :::note
  Recommend to have a Persistent volume(PV) of 20GB, You can start with 1GB for test purposes as well. This PV is used as persistent storage to store the chaos config and chaos-metrics in the Portal. By default, litmus install would use the default storage class to allocate the PV. Provide this value
  :::

- [Helm3](https://v3.helm.sh/) or [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)

## Installation

Installation of Litmus can be done using either of the below methods

- [Helm3](#install-litmus-using-helm) chart
- [Kubectl](#install-litmus-using-kubectl) yaml spec file

### Install Litmus using Helm

The helm chart will install all the required service account configuration and ChaosCenter.

The following steps will help you install Litmus ChaosCenter via helm.

#### Step-1: Add the litmus helm repository

```bash
helm repo add litmuschaos https://litmuschaos.github.io/litmus-helm/
helm repo list
```

#### Step-2: Create the namespace on which you want to install Litmus ChaosCenter

- The litmus infra components will be placed in this namespace.

> The ChaosCenter can be placed in any namespace.

```bash
kubectl create ns <LITMUS_PORTAL_NAMESPACE>
```

#### Step-3: Install Litmus ChaosCenter

```bash
helm install chaos litmuschaos/litmus-2-0-0-beta --namespace=<LITMUS_PORTAL_NAMESPACE> --devel
```

<span style={{color: 'green'}}><b>Expected Output</b></span>

```
NAME: chaos
LAST DEPLOYED: Tue Jun 15 19:20:09 2021
NAMESPACE: <LITMUS_PORTAL_NAMESPACE>
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Thank you for installing litmus-2-0-0-beta 😀

Your release is named chaos and its installed to namespace: litmus.

Visit https://docs.litmuschaos.io/docs/getstarted/ to find more info.
```

> **Note:** Litmus uses Kubernetes CRDs to define chaos intent. Helm3 handles CRDs better than Helm2. Before you start running a chaos experiment, verify if Litmus is installed correctly.

### **Install Litmus using kubectl **

#### **Create the namespace on which you want to install Litmus ChaosCenter**

> If you are installing Litmus in any other namespace apart from `litmus` namespace, make sure to change the same in the manifest too `https://litmuschaos.github.io/litmus/2.0.0-Beta/litmus-2.0.0-Beta.yaml`.

```bash
kubectl create ns <LITMUS_PORTAL_NAMESPACE>
```

#### **Install Litmus ChaosCenter**

Applying the manifest file will install all the required service account configuration and ChaosCenter.

```bash
kubectl apply -f https://litmuschaos.github.io/litmus/2.0.0-Beta/litmus-2.0.0-Beta.yaml -n <LITMUS_PORTAL_NAMESPACE>
```

## **Verify your installation**

---

#### **Verify if the frontend, server, and database pods are running**

- Check the pods in the namespace where you installed Litmus:

  ```bash
  kubectl get pods -n <LITMUS_PORTAL_NAMESPACE>
  ```

  <span style={{color: 'green'}}><b>Expected Output</b></span>

  ```bash
  NAME                                    READY   STATUS  RESTARTS  AGE
  litmusportal-frontend-97c8bf86b-mx89w   1/1     Running 2         6m24s
  litmusportal-server-5cfbfc88cc-m6c5j    2/2     Running 2         6m19s
  mongo-0                                 1/1     Running 0         6m16s
  ```

- Check the services running in the namespace where you installed Litmus:

  ```bash
  kubectl get svc -n <LITMUS_PORTAL_NAMESPACE>
  ```

  <span style={{color: 'green'}}><b>Expected Output</b></span>

  ```bash
  NAME                            TYPE        CLUSTER-IP      EXTERNAL-IP PORT(S)                       AGE
  litmusportal-frontend-service   NodePort    10.100.105.154  <none>      9091:30229/TCP                7m14s
  litmusportal-server-service     NodePort    10.100.150.175  <none>      9002:30479/TCP,9003:31949/TCP 7m8s
  mongo-service                   ClusterIP   10.100.226.179  <none>      27017/TCP                     7m6s
  ```

---

#### **Verify Successful Registration of the Self Agent post [Account Configuration](setup-without-ingress)**

Once the project is created, the cluster is automatically registered as a chaos target via installation of [ChaosAgents](../getting-started/resources#chaosagents.md). This is represented as [Self-Agent](../getting-started/resources#types-of-chaosagents.md) in [ChaosCenter](../getting-started/resources#chaoscenter.md).

```bash
kubectl get pods -n litmus
```

```bash
NAME                                     READY   STATUS    RESTARTS   AGE
argo-server-58cb64db7f-pmbnq             1/1     Running   0          5m32s
chaos-exporter-547b59d887-4dm58          1/1     Running   0          5m27s
chaos-operator-ce-84ddc8f5d7-l8c6d       1/1     Running   0          5m27s
event-tracker-5bc478cbd7-xlflb           1/1     Running   0          5m28s
litmusportal-frontend-97c8bf86b-mx89w    1/1     Running   0          15m
litmusportal-server-5cfbfc88cc-m6c5j     2/2     Running   1          15m
mongo-0                                  1/1     Running   0          15m
subscriber-958948965-qbx29               1/1     Running   0          5m30s
workflow-controller-78fc7b6c6-w82m7      1/1     Running   0          5m32s
```

## Resources

<iframe width="560" height="315" src="https://www.youtube.com/embed/rOrKegj5ePI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Learn More

- [Install ChaosCenter in Namespace Scope](chaoscenter-namespace-scope-installation)
- [Setup Endpoints and Access ChaosCenter without Ingress](setup-without-ingress)
- [Setup Endpoints and Access ChaosCenter with Ingress](setup-with-ingress)
