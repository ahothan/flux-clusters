# FLUX LEVEL 1 CLUSTERS REPOSITORY

This is an example of level 1 repository that contains the cluster paths that Flux controller on Kubernetes clusters reconcile from.
There can be as many clusters repository as needed, generally 1 per group of clusters managed by a common authority, for example:

- 1 clusters repository for production clusters owned by operations and release engineering 
- 1 clusters repository for dev/test/staging clusters owned by dev and QA teams

Each cluster is associated to:

- one blueprint to deploy, defined in the level 2 blueprints repository
- one set of configuration variables for the blueprint to deploy, defined in this repository

## Cluster paths

Clusters are grouped by Data Center (DC).
Each cluster is associated to a folder under the associated DC (e.g. clusters/SJC/sjc1/flux-system).
Such cluster folder is usually created by a flux bootstrap command using the Flux cli and subsequently customized to:

- point to a blueprint to deploy,
- define cluster specific configuration variables.

## Associating a blueprint to a cluster

The cluster-blueprint.yaml file describes the blueprint to deploy on each cluster by specifying:

- the blueprints git repository URL
- the blueprint path in that git repository
- the git branch or tag to use in that git repository

## Flux substitution variables

Blueprint configuration variables are Flux substitution variables defined in ConfigMaps.
There are 3 levels of configuration variable overlays, from highest to lowest precedence order:

- cluster level variables specific to each cluster (cluster-vars.yaml)
- DC level variables shared by all clusters in same DC (dc-vars.yaml)
- global variables shared by all clusters (global-vars.yaml)

## Quick start guide

### Pre-requisites

It is assumed you have:

- a k8s cluster and KUBECONFIG set accordingly
- flux cli installed (version 2.2.3+)
- fork this git repository
- GITHUB_TOKEN must be set to your PAT (Personal Access Token) with admin permission
- USER variable contains your github personal account name

### Bootstrap

You can pick any cluster path available to test. For example, to deploy the blueprint associated to the SFO/sfo1 cluster:

```text
flux bootstrap github --private=false --personal=true --owner=$USER --repository=flux-clusters --path=clusters/SFO/sfo1
```

Example of run output:

```text
► connecting to github.com
► cloning branch "main" from Git repository "https://github.com/ahothan/flux-clusters.git"
✔ cloned repository
► generating component manifests
✔ generated component manifests
✔ component manifests are up to date
► installing components in "flux-system" namespace
✔ installed components
✔ reconciled components
► determining if source secret "flux-system/flux-system" exists

...

✔ reconciled source secret
► generating sync manifests
✔ generated sync manifests
✔ sync manifests are up to date
► applying sync manifests
✔ reconciled sync configuration
◎ waiting for GitRepository "flux-system/flux-system" to be reconciled
✔ GitRepository reconciled successfully
◎ waiting for Kustomization "flux-system/flux-system" to be reconciled
✔ Kustomization reconciled successfully
► confirming components are healthy
✔ helm-controller: deployment ready
✔ kustomize-controller: deployment ready
✔ notification-controller: deployment ready
✔ source-controller: deployment ready
✔ all components are healthy
```

Resulting pods in the podinfo namespace:

```text
$ kubectl get pod -n podinfo
NAME                       READY   STATUS    RESTARTS   AGE
podinfo-5dbd6f94d8-5bqhf   1/1     Running   0          22s
```

### Change configuration

You can modify any configuration variable in any of the configmap files, commit changes and let Flux reconcile.
For example set the number or replicas to 3 and enable Redis (similar to values in SJC/sjc1/cluster-vars.yaml).

