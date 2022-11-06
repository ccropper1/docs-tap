# Create Service Accounts

You can create two types of service accounts:

1. Read-only service account - only able to use `GET` API requests
1. Read-write service account - full access to the API requests

## <a id='ro-serv-accts'></a>Read-only service account

### With default cluster role

As a part of the Store installation, the `metadata-store-read-only` cluster role
is created by default. This cluster role allows the bound user to have `get`
access to all resources. To bind to this cluster role, run the following command
depending on the Kubernetes version:

```console
kubectl apply -f - -o yaml << EOF
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: metadata-store-ready-only
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: metadata-store-read-only
subjects:
- kind: ServiceAccount
  name: metadata-store-read-client
  namespace: metadata-store
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: metadata-store-read-client
  namespace: metadata-store
automountServiceAccountToken: false
EOF
```

If you do not want to bind to a cluster role, create your own read-only role in the `metadata-store` namespace with a service account. The following example command creates a service account named `metadata-store-read-client`:

- Kubernetes version v1.24 or later:

    ```console
    kubectl apply -f - -o yaml << EOF
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
      name: metadata-store-ready-only
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: metadata-store-read-only
    subjects:
    - kind: ServiceAccount
      name: metadata-store-read-client
      namespace: metadata-store
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: metadata-store-read-client
      namespace: metadata-store
      annotations:
        kapp.k14s.io/change-group: "metadata-store.apps.tanzu.vmware.com/service-account"
    automountServiceAccountToken: false
    ---
    apiVersion: v1
    kind: Secret
    type: kubernetes.io/service-account-token
    metadata:
      name: metadata-store-read-client
      namespace: metadata-store
      annotations:
        kapp.k14s.io/change-rule: "upsert after upserting metadata-store.apps.tanzu.vmware.com/service-account"
        kubernetes.io/service-account.name: "metadata-store-read-client"
    EOF
    ```

  >**Note** For Kubernetes v1.24 and later, services account secrets are no
  >longer automatically created. The service account secret must be manually
  >created.

### With custom cluster role

If you do not want to bind to the default cluster role, create a read-only role
in the `metadata-store` namespace with a service account. The following example
command creates a service account named `metadata-store-read-client`, depending
on the Kubernetes version:

- Kubernetes v1.24 or earlier:

    ```console
    kubectl apply -f - -o yaml << EOF
    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
      name: metadata-store-ro
      namespace: metadata-store
    rules:
    - resources: ["all"]
      verbs: ["get"]
      apiGroups: [ "metadata-store/v1" ]
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      name: metadata-store-ro
      namespace: metadata-store
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: Role
      name: metadata-store-ro
    subjects:
    - kind: ServiceAccount
      name: metadata-store-read-client
      namespace: metadata-store
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: metadata-store-read-client
      namespace: metadata-store
    automountServiceAccountToken: false
    EOF
    ```

- Kubernetes 1.24 or later:

    ```console
    kubectl apply -f - -o yaml << EOF
    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
      name: metadata-store-ro
      namespace: metadata-store
    rules:
    - resources: ["all"]
      verbs: ["get"]
      apiGroups: [ "metadata-store/v1" ]
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      name: metadata-store-ro
      namespace: metadata-store
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: Role
      name: metadata-store-ro
    subjects:
    - kind: ServiceAccount
      name: metadata-store-read-client
      namespace: metadata-store
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: metadata-store-read-client
      namespace: metadata-store
      annotations:
        kapp.k14s.io/change-group: "metadata-store.apps.tanzu.vmware.com/service-account"
    automountServiceAccountToken: false
    ---
    apiVersion: v1
    kind: Secret
    type: kubernetes.io/service-account-token
    metadata:
      name: metadata-store-read-client
      namespace: metadata-store
      annotations:
        kapp.k14s.io/change-rule: "upsert after upserting metadata-store.apps.tanzu.vmware.com/service-account"
        kubernetes.io/service-account.name: "metadata-store-read-client"
    EOF
    ```

    > **Note** For Kubernetes v1.24 and later, services account secrets are no
    > longer automatically created. The service account secret must be
    > manually created.

## Read-write service account

The following command creates a service account called
`metadata-store-read-write-client`, depending on the Kubernetes version. To
create a read-write service account, run:

- Kubernetes version before 1.24:

    ```console
    kubectl apply -f - -o yaml << EOF
    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
      name: metadata-store-read-write
      namespace: metadata-store
    rules:
    - resources: ["all"]
      verbs: ["get", "create", "update"]
      apiGroups: [ "metadata-store/v1" ]
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      name: metadata-store-read-write
      namespace: metadata-store
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: Role
      name: metadata-store-read-write
    subjects:
    - kind: ServiceAccount
      name: metadata-store-read-write-client
      namespace: metadata-store
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: metadata-store-read-write-client
      namespace: metadata-store
    automountServiceAccountToken: false
    EOF
    ```

- Kubernetes v1.24 or later:

    ```console
    kubectl apply -f - -o yaml << EOF
    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
      name: metadata-store-read-write
      namespace: metadata-store
    rules:
    - resources: ["all"]
      verbs: ["get", "create", "update"]
      apiGroups: [ "metadata-store/v1" ]
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      name: metadata-store-read-write
      namespace: metadata-store
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: Role
      name: metadata-store-read-write
    subjects:
    - kind: ServiceAccount
      name: metadata-store-read-write-client
      namespace: metadata-store
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: metadata-store-read-write-client
      namespace: metadata-store
      annotations:
        kapp.k14s.io/change-group: "metadata-store.apps.tanzu.vmware.com/service-account"
    automountServiceAccountToken: false
    ---
    apiVersion: v1
    kind: Secret
    type: kubernetes.io/service-account-token
    metadata:
      name: metadata-store-read-write-client
      namespace: metadata-store
      annotations:
        kapp.k14s.io/change-rule: "upsert after upserting metadata-store.apps.tanzu.vmware.com/service-account"
        kubernetes.io/service-account.name: "metadata-store-read-write-client"
    EOF
    ```
  
  >**Note** For Kubernetes v1.24, services account secrets are no longer
  >automatically created. The service account secret must be manually
  >created.
  
## Getting the Access Token

To retrieve the read-only access token, run:

```console
kubectl apply -f - -o yaml << EOF
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: metadata-store-read-write
  namespace: metadata-store
rules:
- resources: ["all"]
  verbs: ["get", "create", "update"]
  apiGroups: [ "metadata-store/v1" ]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: metadata-store-read-write
  namespace: metadata-store
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: metadata-store-read-write
subjects:
- kind: ServiceAccount
  name: metadata-store-read-write-client
  namespace: metadata-store
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: metadata-store-read-write-client
  namespace: metadata-store
automountServiceAccountToken: false
EOF
```

## <a id='get-access-token'></a>Getting the Access Token
To retrieve the read-only access token, run the following command:

```console
kubectl get secret $(kubectl get sa -n metadata-store metadata-store-read-client -o json | jq -r '.secrets[0].name') -n metadata-store -o json | jq -r '.data.token' | base64 -d
```

To retrieve the read-write access token run the following command:

```console
kubectl get secret $(kubectl get sa -n metadata-store metadata-store-read-write-client -o json | jq -r '.secrets[0].name') -n metadata-store -o json | jq -r '.data.token' | base64 -d
```

The access token is a "Bearer" token used in the http request header
"Authorization." For example, `Authorization: Bearer
eyJhbGciOiJSUzI1NiIsImtpZCI6IjhMV0...`.