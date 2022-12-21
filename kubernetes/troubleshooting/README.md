# Troubleshooting notes

## Forcefully deleting namespaces stuck at `Terminating` state
1. Delete a single `Terminating` namespace
```
    python3 force-delete-k8s-namespace.py <namespace-to-delete>
```
2. Deleting all `Terminating` namespaces at once
```
    kubectl get ns | grep Terminating | awk '{print $1}' | xargs -I '{}' python3 force-delete-k8s-namespace.py {}