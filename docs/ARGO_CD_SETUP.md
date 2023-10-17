# ![Argo CD Logo](images/argo-cd-logo.png) Argo CD Setup Guide

This document explains how to set up Argo CD for Unguard.

## 🗒️ Prerequisites

* [Kubectl](https://kubernetes.io/docs/tasks/tools/)

## Setup Argo CD

### 1. Install Argo CD as described in the [documentation](https://argo-cd.readthedocs.io/en/stable/#quick-start).

### 2. Enable orchestrating synchronization using "sync waves" for the app-of-apps pattern.

To ensure that MariaDB is installed before Unguard we utilize Argo CD's [sync waves](https://argo-cd.readthedocs.io/en/stable/user-guide/sync-waves/).
To enable them for the [app-of-apps](https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/#app-of-apps-pattern)
pattern Argo CD needs to be configured as described [here](https://argo-cd.readthedocs.io/en/stable/operator-manual/health/#argocd-app).

TLDR, apply the following command:

```shell
kubectl apply -n argocd -f - <<EOF
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
  labels:
    app.kubernetes.io/name: argocd-cm
    app.kubernetes.io/part-of: argocd
data:
  resource.customizations: |
    argoproj.io/Application:
     health.lua: |
       hs = {}
       hs.status = "Progressing"
       hs.message = ""
       if obj.status ~= nil then
         if obj.status.health ~= nil then
           hs.status = obj.status.health.status
           if obj.status.health.message ~= nil then
             hs.message = obj.status.health.message
           end
         end
       end
       return hs
EOF
```

### 3. Add `unguard-infra` repository to Argo CD.

```shell
kubectl apply -n argocd -f - <<EOF
apiVersion: v1
kind: Secret
metadata:
  name: unguard-infra
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: repository
stringData:
  url: https://github.com/MfCrizz/unguard-infra.git
  username: not-used
EOF
```

### 5. Apply root application

Apply the root Argo CD application resource for your cluster.

```shell
kubectl apply -n argocd -f unguard/unguard-root.yaml
```
