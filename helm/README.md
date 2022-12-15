# Helm Cheatsheet

## Basic - Working with helm command
1. Print out all helm charts added to local machine:        
```
    helm search repo
```
```
    # output
    NAME                                                    CHART VERSION   APP VERSION             DESCRIPTION                                       
    prometheus-community/alertmanager                       0.22.0          v0.24.0                 The Alertmanager handles alerts sent by client ...
    prometheus-community/jiralert                           1.0.0           1.2                     A Helm chart for Kubernetes to install jiralert  
    ... 
```
2. Print out all helm charts in [the Artifact Hub](https://artifacthub.io/packages/search?kind=0):      
```
    helm chart hub
```
3. For searching some chart (e.g. localstack) in local repository or online use:
```
    # local repository search
    helm search repo localstack
    # artifact hub search
    helm search hub localstack
```
4. Adding additional helm repository can be done by
```
    helm repo add localstack-charts https://localstack.github.io/helm-charts
    # output
    "localstack-charts" has been added to your repositories

    helm search repo localstack-charts
    # output
    NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
    NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
    localstack-charts/localstack    0.5.2           latest          LocalStack - a fully functional local AWS cloud...
```
5. To install a new package, I use `helm install` command. At least it takes two arguments: A release name and
the name of the chart I want to install.
```
    helm install localstack localstack-charts/localstack
    # output
    NAME: localstack
    LAST DEPLOYED: Thu Dec  8 10:42:58 2022
    NAMESPACE: default
    STATUS: deployed
    REVISION: 1
    NOTES:
    1. Get the application URL by running these commands:
    export NODE_PORT=$(kubectl get --namespace "default" -o jsonpath="{.spec.ports[0].nodePort}" services localstack)
    export NODE_IP=$(kubectl get nodes --namespace "default" -o jsonpath="{.items[0].status.addresses[0].address}")
    echo http://$NODE_IP:$NODE_PORT 
```
> Helm installs the resources in a certain order

6. To keep track of a release's state, or to re-read configuration information, you can use helm status:
```
    helm status localstack
```
7. I can see the values of a Chart with:
```
    helm show values localstack-charts/localstack
```
8. To customize the default values of a Chart, I modify and then install with this modified values file:
```
    # save a copy of default values
    helm show values localstack-charts/localstack > values.yaml
    # modify values.yaml
    vim values.yaml
    # install the Chart with this modified values.yaml file
    helm install localstack localstack-charts/localstack -f values.yaml
```
> By default the above `helm install` commands may install the Chart in `default` namespace, if the Chart 
> is not configured to be installed in a certain namespace
> To install it in a namespace, add `----namespace <NAMESPACE>` flag.
        
9. When a new version of a *chart is released*, or when I want to *change the configuration* of your release, I can use the helm upgrade command.
```
    helm upgrade localstack localstack-charts/localstack  -f values.yaml 
```
10. If upgrading or changing configuration goes wrong, I can rollback the `release` to a certain `revision`. To display the revisions of a chart
run  `helm history [RELEASE]` the command. For example rollback `localstack` release to its `1` revision:
```
    helm rollback localstack 1
```
11. To uninstall a release, use `helm uninstall [RELEASE]`, E.g:
```
    helm uninstall localstack
```
12. Show local repositories list `helm repo list`:
```
    # output
    NAME                    URL                                               
    pingcap                 https://charts.pingcap.org/                       
    bitnami                 https://charts.bitnami.com/bitnami                
    grafana                 https://grafana.github.io/helm-charts             
    prometheus-community    https://prometheus-community.github.io/helm-charts
    vm                      https://victoriametrics.github.io/helm-charts/    
    nats                    https://nats-io.github.io/k8s/helm/charts/        
    brigade                 https://brigadecore.github.io/charts              
    localstack-charts       https://localstack.github.io/helm-charts 

```
13. List all installed charts in k8s, use `helm list --all-namespaces`:
14. To list releases, show theis statuses or history, add `--namespace [NAMESPACE]`
## Intermediate - Developing charts