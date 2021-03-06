# Snapshot Example

### Minimum Kubernetes Version: v1.17.0
refer to [Snapshot & Restore Feature](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) for more details.

## Install CSI Driver

Follow the [instructions](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/install-csi-driver-master.md) to install snapshot driver.

## Create a Source PVC

```console
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/storageclass-azuredisk-csi.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml
```

### Check the Source PVC

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk
lost+found
outfile
```

## Create a Snapshot of the Source PVC

```console
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml
```
### Check the Creation Status

```console
$ kubectl describe volumesnapshot azuredisk-volume-snapshot
Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"snapshot.storage.k8s.io/v1beta1","kind":"VolumeSnapshot","metadata":{"annotations":{},"name":"azuredisk-volume-snapshot","n...
API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-02-04T13:59:54Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  48044
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               2b0ef334-4112-4c86-8360-079c625d5562
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-2b0ef334-4112-4c86-8360-079c625d5562
  Creation Time:                       2020-02-04T14:23:36Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

## Restore the Snapshot into a New PVC

```console
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml
```

### Check Sample Data

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk
lost+found
outfile
```
