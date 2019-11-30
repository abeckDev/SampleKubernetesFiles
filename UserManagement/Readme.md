# Create User in Kubernetes

Guide to create a new User in Athlon K8s Kubernetes Cluster

## Create Service Account

First of all you need a Service Account. This is the Account for the person who wants access to the Kubernetes Cluster. 

Every ServiceAccount contains a Token. If you grant Permissions to administer the System to the Service Account you can access the Kubernetes Dashboard with the Token. 

We are creating Service Account with name admin-user in namespace kube-system first.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user # Name des Serviceaccounts
  namespace: kube-system # namespace
```

## Create Cluster Role Binding

A Role binding means granting access for a service account to the Kubernetes Cluster.

In most cases the admin Role already exists in the cluster. We can use it and create only RoleBinding for our ServiceAccount.

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: admin-user # Name des Role Bindings
roleRef: # Gibt an welche Berechtigung verwendet werden soll
  apiGroup: rbac.authorization.k8s.io # Nutzt die Berechtigungen, welche in der API Group definiert sind
  kind: ClusterRole # Typ der verwendeten Rollen für die zuweisung ist ClusterRole
  name: cluster-admin # Name der zuzuweisenden Rolle für den Service Account
subjects: # Gibt an, wem die Rolle zugewiesen wird
- kind: ServiceAccount # 
  name: admin-user
  namespace: kube-system
```

## Bearer Token 

Now we need to find token we can use to log in. Execute following command:

```bash
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```

It should print something like:

```text
Name:         admin-user-token-6gl6l
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name=admin-user
              kubernetes.io/service-account.uid=b16afba9-dfec-11e7-bbb9-901b0e532516

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLTZnbDZsIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJiMTZhZmJhOS1kZmVjLTExZTctYmJiOS05MDFiMGU1MzI1MTYiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06YWRtaW4tdXNlciJ9.M70CU3lbu3PP4OjhFms8PVL5pQKj-jj4RNSLA4YmQfTXpPUuxqXjiTf094_Rzr0fgN_IVX6gC4fiNUL5ynx9KU-lkPfk0HnX8scxfJNzypL039mpGt0bbe1IXKSIRaq_9VW59Xz-yBUhycYcKPO9RM2Qa1Ax29nqNVko4vLn1_1wPqJ6XSq3GYI8anTzV8Fku4jasUwjrws6Cn6_sPEGmL54sq5R4Z5afUtv-mItTmqZZdxnkRqcJLlg2Y8WbCPogErbsaCDJoABQ7ppaqHetwfM_0yMun6ABOQbIwwl8pspJhpplKwyo700OSpvTT9zlBsu-b35lzXGBRHzv5g_RA
```

With that token the user can now login to the Kubernetes Dashboard. 