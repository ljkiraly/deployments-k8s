# Test kernel to Ethernet to kernel connection

This is a topology with more elements to reproduce memory leak in nsmgr.

## Requires

kubectl apply -k metrics-server

Make sure that you have completed steps from [basic](../../basic)  setup.

## Run

Scale the registry:

```bash
kubectl scale deployment -n nsm-system  registry-k8s --replicas=2
```

Wait for registry to be ready:

```bash
kubectl wait --for=condition=ready --timeout=1m pod -l app=registry -n nsm-system
```

Deploy NSC and NSE:

```bash
kubectl apply -k .
```

Wait for applications ready:

```bash
kubectl wait --for=condition=ready --timeout=1m pod -l app=nsc-kernel -n ns-2registry3nsexnsc
```

```bash
kubectl wait --for=condition=ready --timeout=1m pod -l app=nse-kernel-1 -n ns-2registry3nsexnsc
```

```bash
kubectl wait --for=condition=ready --timeout=1m pod -l app=nse-kernel-2 -n ns-2registry3nsexnsc
```

Scale out and in in infinite loop

```bash
while :; do kubectl scale deployment -n ns-2registry3nsexnsc nsc-kernel --replicas=6; sleep 10; kubectl scale deployment -n ns-2registry3nsexnsc nsc-kernel --replicas=2; sleep 10; date; for nsmgr in `kubectl get pods -n nsm-system | grep nsmgr | cut -f1 -d' '`; do kubectl top -n nsm-system pod $nsmgr; done; done
```

## Cleanup

Delete ns:

```bash
kubectl delete ns ns-2registry3nsexnsc
```
