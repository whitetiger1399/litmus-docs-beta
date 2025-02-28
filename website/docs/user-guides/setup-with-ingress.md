---
id: setup-with-ingress
title: Install LitmusPortal with Ingress
sidebar_label: Setup With Ingress
---

---

## Prerequisites

Before setting up endpoint with Ingress make sure the [Litmus ChaosCenter](../getting-started/resources#chaoscenter.md) is installed in either one of these scopes

- [Cluster Scope](chaoscenter-cluster-scope-installation)
- [Namespace Scope](chaoscenter-namespace-scope-installation)

## Install ChaosCenter with Ingress

With Litmus-2.0.0-Beta3, ChaosCenter can be installed with ingress.
In the following doc, we will use the Nginx ingress controller for ingress setup.

1. By default, the service type is `NodePort`. For Ingress, we need to change the service type to `ClusterIP` in the following services.

- `litmusportal-frontend-service`
- `litmusportal-server-service`

2. Install Nginx Ingress Controller along with Kubernetes RBAC roles and bindings, please refer [here](https://kubernetes.github.io/ingress-nginx/deploy/#installation-guide)

> - If you're changing ingress name from **litmus-ingress** to a different name, make sure to update the **INGRESS_NAME** environment variable in the litmusportal-server deployment
> - Set the environment variable **INGRESS** as true in the litmusportal-server deployment.

### With HTTP

Sample litmus ingress manifest With HTTP

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
  name: litmus-ingress
spec:
  rules:
    - host: '<HOST-NAME>'
      http:
        paths:
          - backend:
              serviceName: litmusportal-frontend-service
              servicePort: 9091
            path: /(.*)
            pathType: ImplementationSpecific
          - backend:
              serviceName: litmusportal-server-service
              servicePort: 9002
            path: /backend/(.*)
            pathType: ImplementationSpecific
```

```bash
kubectl apply -f <litmus_ingress_manifest> -n <PORTAL_NAMESPACE>
```

### With HTTPS

1. Install CertManager

```bash
kubectl create namespace cert-manager
helm repo add jetstack https://charts.jetstack.io
helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.3.0 --set installCRDs=true
```

2. Install LetsEncrypt Cluster Issuer

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
   name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: "your@mail.com"
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
     	ingress:
        class: nginx
```

3. Sample Litmus Portal Ingress Manifest with HTTPS

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
  labels:
    component: litmusportal-frontend
  name: litmusportal-ingress
  namespace: litmus
spec:
  rules:
    - host: '<HOST-NAME>'
      http:
        paths:
          - backend:
              serviceName: litmusportal-frontend-service
              servicePort: 9091
            path: /(.*)
            pathType: ImplementationSpecific
          - backend:
              serviceName: litmusportal-server-service
              servicePort: 9002
            path: /backend/(.*)
            pathType: ImplementationSpecific
  tls:
    - hosts:
        - '<HOST-NAME>'
      secretName: litmuspreview-tls-secret
```

```bash
kubectl apply -f <litmus_ingress_manifest> -n <PORTAL_NAMESPACE>
```

## Learn More

- [Setup Endpoints and Access ChaosCenter without Ingress](setup-without-ingress)
- [Install ChaosCenter in Namespace Scope](chaoscenter-namespace-scope-installation)
- [Install ChaosCenter in Cluster Scope](chaoscenter-cluster-scope-installation)
