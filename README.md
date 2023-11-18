# DirectPV ARM64


# Just my work on getting DirectPV working on my ARM64 Kubernetes cluster (Might not work)

#
# What i had, but didn't work.
#
```
kubectl directpv install

 ███████████████████████████████████████████████████████████████████████████ 100%

┌──────────────────────────────────────┬──────────────────────────┐
│ NAME                                 │ KIND                     │
├──────────────────────────────────────┼──────────────────────────┤
│ directpv                             │ Namespace                │
│ directpv-min-io                      │ ServiceAccount           │
│ directpv-min-io                      │ ClusterRole              │
│ directpv-min-io                      │ ClusterRoleBinding       │
│ directpv-min-io                      │ Role                     │
│ directpv-min-io                      │ RoleBinding              │
│ directpvdrives.directpv.min.io       │ CustomResourceDefinition │
│ directpvvolumes.directpv.min.io      │ CustomResourceDefinition │
│ directpvnodes.directpv.min.io        │ CustomResourceDefinition │
│ directpvinitrequests.directpv.min.io │ CustomResourceDefinition │
│ directpv-min-io                      │ CSIDriver                │
│ directpv-min-io                      │ StorageClass             │
│ node-server                          │ Daemonset                │
│ controller                           │ Deployment               │
└──────────────────────────────────────┴──────────────────────────┘

DirectPV installed successfully

# kubectl get all -n directpv
NAME                              READY   STATUS             RESTARTS      AGE
pod/controller-545d766d46-fvmmw   0/3     CrashLoopBackOff   2 (16s ago)   19s
pod/controller-545d766d46-lbbth   0/3     CrashLoopBackOff   2 (14s ago)   19s
pod/controller-545d766d46-r7bqb   0/3     CrashLoopBackOff   2 (15s ago)   19s
pod/node-server-9tcg5             1/4     CrashLoopBackOff   2 (13s ago)   19s
pod/node-server-dz56k             1/4     Error              4 (16s ago)   19s
pod/node-server-fmd4b             1/4     CrashLoopBackOff   2 (15s ago)   19s
pod/node-server-hfdkp             1/4     CrashLoopBackOff   2 (16s ago)   20s
pod/node-server-hgb5w             1/4     Error              4 (16s ago)   20s
pod/node-server-p9qth             1/4     CrashLoopBackOff   2 (14s ago)   19s
pod/node-server-pprp7             1/4     CrashLoopBackOff   2 (15s ago)   20s
pod/node-server-wjp8x             1/4     Error              4 (16s ago)   19s
pod/node-server-xf55q             1/4     CrashLoopBackOff   2 (16s ago)   19s

NAME                         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/node-server   9         9         0       9            0           <none>          20s

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/controller   0/3     3            0           20s

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/controller-545d766d46   3         3         0       20s

kubectl logs -n directpv pod/node-server-hfdkp
Defaulted container "node-driver-registrar" out of: node-driver-registrar, node-server, node-controller, liveness-probe
exec /csi-node-driver-registrar: exec format error

# Images
quay.io/minio/csi-node-driver-registrar:v2.8.0
quay.io/minio/csi-provisioner:v3.5.0
quay.io/minio/livenessprobe:v2.10.0
quay.io/minio/csi-resizer:v1.8.0
quay.io/minio/directpv:latest or quay.io/minio/directpv:v4.0.9-arm64
```
#
# What i build/reverse enigneerd/tinkerd
#
``` 
podman login docker.io
Username: moki38
Password:
Login Succeeded!

https://github.com/kubernetes-csi/livenessprobe.git
make
cp Dockerfile Containerfile
podman build -t moki38/livenessprobe:latest -t moki38/livenessprobe:v2.11.0 .
podman image push moki38/livenessprobe:latest
podman image push moki38/livenessprobe:v2.11.0

git clone https://github.com/kubernetes-csi/node-driver-registrar.git
make
cp Dockerfile Containerfile
podman build -t moki38/csi-node-driver-registrar:latest -t moki38/csi-node-driver-registrar:v2.9.1 .
podman image push moki38/csi-node-driver-registrar:latest
podman image push moki38/csi-node-driver-registrar:v2.9.1

https://github.com/kubernetes-csi/external-provisioner.git
make
cp Dockerfile Containerfile
podman build -t moki38/csi-provisioner:latest -t moki38/csi-provisioner:v3.6.2 .
podman image push moki38/csi-provisioner:latest
podman image push moki38/csi-provisioner:v3.6.2

https://github.com/kubernetes-csi/external-resizer.git
make
cp Dockerfile Containerfile
podman build -t moki38/csi-resizer:latest -t moki38/csi-resizer:v1.9.2 .
podman image push moki38/csi-resizer:latest
podman image push moki38/csi-resizer:v1.9.2

image="quay.io/minio/directpv:v4.0.9-arm64"
podman pull $image
podman tag $image `echo $image | sed -E 's#^[^/]+/#moki38/#'`
podman push `echo $image | sed -E 's#^[^/]+/#moki38/#'`
```

#
# Where i am
#
```
kubectl create  -f directpv-install.yaml
```

```
kubectl get all -n directpv

NAME                              READY   STATUS    RESTARTS   AGE
pod/controller-85df994996-48pkq   2/3     Running   0          32s
pod/controller-85df994996-hq6f8   2/3     Running   0          32s
pod/controller-85df994996-px8p5   2/3     Running   0          32s
pod/node-server-4nv2c             3/4     Running   0          32s
pod/node-server-4p95b             3/4     Running   0          32s
pod/node-server-4w6rv             3/4     Running   0          32s
pod/node-server-6zlzw             3/4     Running   0          32s
pod/node-server-986sp             3/4     Running   0          32s
pod/node-server-bdsbs             3/4     Running   0          32s
pod/node-server-fqprr             3/4     Running   0          32s
pod/node-server-jx5f6             3/4     Running   0          32s
pod/node-server-xvkhq             3/4     Running   0          32s

NAME                         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/node-server   9         9         0       9            0           <none>          32s

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/controller   0/3     3            0           32s
```
NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/controller-85df994996   3         3         0       32s


