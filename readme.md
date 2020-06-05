
# Helm 3 - basics
## why helm

**Packaging**

> With kubectl you do not install application as atomic set of kubernetes objects. Rather you deploy each object (yaml) separately. Also these objects might have dependencies between each other and order of installation might be important. Helm allows to group all kuberentes objects (yaml files) in one package and install the whole package.

**Versioning**

> kubectl does not support versioning of kubernets objects

## chart
With helm you install your application as an entity defined by your **chart** and not a set of independent kubernetes objects. Chart is definition of your application.

## persistency

Helm stores release manifests (application versions) inside Kubernetes as secrets.

## three-way merge patches update

What if I modify Kubernetes objects with a tools other then Helm like kubectl. Helm tree compares the three manifests: old chart, new chart, live state. Based on this it creates a patch that merges the updates in best possible way. For example if something has been changed via kubectl and now we want apply a new chart then the change that was done via kubectl will stay as long as there is no conflict with new chart. The same is relvant also for rollbacks!

![3-way-merge](images/3-way-merge.png)

## namespaces

Helm supports namespaces like kubectl.

# Installing a local K8s cluster with Helm

![local-k8s-and-helm](images/local-k8s-and-helm.png)

# links
https://app.pluralsight.com/library/courses/kubernetes-packaging-applications-helm/exercise-files
https://github.com/phcollignon/helm3