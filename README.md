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

