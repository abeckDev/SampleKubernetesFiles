# Ingress Controller

To use an Ingress in your Cluster you need to extend it with an Ingress Controller. 

One of the most common one ist NGINX Ingress Controller

## Installation

### Make sure Helm ist initialized correctly

```shell
helm init
```

### Create a namespace for the ingress controller

```shell
kubectl create ns ingress-controller
```

### Install Ingress Controller in Ingress Namespace

```shell
helm install stable/nginx-ingress --name ingress-controller --namespace ingress-controller --set controller.replicaCount=2
```

This will install and register the Nginx Ingress Controller in your Cluster and also akquire an IP Address. This IP Address is the one on which you have to point your DNS Entries in order to manage them with the Ingress Controller. 

### (Optional) SetUp Lets Encrypt Support

#### Install the Cert Manager Resource Definitions

```shell
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.7/deploy/manifests/00-crds.yaml
```

#### Create the namespace for cert-manager

```shell
kubectl create namespace cert-manager
```

#### Label the cert-manager namespace to disable resource validation

```shell
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true
```

#### Add the Jetstack Helm repository

```shell
helm repo add jetstack https://charts.jetstack.io
```

#### Update your local Helm chart repository cache

```shell
helm repo update
```

#### Install the cert-manager Helm chart

```shell
helm install --name cert-manager --namespace cert-manager --version v0.7.0 jetstack/cert-manager
```

#### Install LetsEncrypt Driver

To generate Certificate you need a driver for e.g. LetsEncrypt. Such Drivers are called ClusterIssuer

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
---
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-prod
    http01: {}
```

## Use Ingress Controller

Now the Ingress Controller is ready and can be used like the following:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: auth-service-tls-cert # Name of Certificate
  namespace: default
spec:
  secretName: auth-service-tls-cert # Name of secret which will be created and contains the created certificate
  dnsNames:
  - login.dlrg-dresden.net 
  acme:
    config:
    - http01:
        ingressClass: nginx # Ingress Class which is used to solve acme
      domains:
      - login.dlrg-dresden.net
  issuerRef:
    name: letsencrypt-prod # Which issuer to use
    kind: ClusterIssuer
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com # The Domain which is attached to the pubIp of the ingress Controller
    secretName: tls-secret # secret name where certificate is stored
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com # Regeln für diese Domain erstellen
    http:
      paths:
      - path: / # Pfad der Regel
        backend:
          serviceName: aks-helloworld # Regel wird auf diesen Service angewand
          servicePort: 80 # Port vom Service
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```
