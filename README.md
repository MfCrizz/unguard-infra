# ![Unguard Logo](docs/images/unguard-logo.png) + ![Argo CD Logo](docs/images/argo-cd-logo.png) ArgoCD Application Definition for Unguard

> **Note**
> This guide assumes that Argo CD is installed and configured as described [here](/docs/ARGO_CD_SETUP.md).

This repository holds the Argo CD configuration for an Unguard cluster.

We utilize the [app-of-apps](https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/#app-of-apps-pattern) pattern,
where each cluster features a root application definition that instructs Argo CD to install and synchronise all applications located in the corresponding `/applications/` subfolder.

The configuration structure for the `unguard` cluster looks like this:

```text
└── unguard
   ├── applications
   │   ├── unguard-app.yaml
   │   └── unguard-mariadb.yaml
   ├── unguard-root.yaml
   └── values.yaml
```

## How to Make Changes to a Cluster

Argo CD monitors this repository and automatically applies changes to the clusters. To modify an installation of Unguard,
create a branch and pull request with your changes. They will be automatically applied by Argo CD once they have been merged in the main branch.

1. Create branch.
2. Make the required changes to the cluster configuration.
3. Open pull request.
4. Once merged, changes get applied by Argo CD.

### How to Change the Version of Unguard

1. Create branch.
2. Change `targetRevision` in `*-app.yaml` to the version you want to deploy. \
3. Open pull request.
4. Once merged, the desired version of Unguard gets automatically deployed by Argo CD.

## How to add a Cluster

1. Create configuration for the cluster, similar to the existing.
2. Install Argo CD on the cluster as described in [here](/docs/ARGO_CD_SETUP.md).
