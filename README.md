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

git clone https://github.com/kubernetes-csi/livenessprobe.git
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

git clone https://github.com/kubernetes-csi/external-provisioner.git
make
cp Dockerfile Containerfile
podman build -t moki38/csi-provisioner:latest -t moki38/csi-provisioner:v3.6.2 .
podman image push moki38/csi-provisioner:latest
podman image push moki38/csi-provisioner:v3.6.2

git clone https://github.com/kubernetes-csi/external-resizer.git
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

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/controller-85df994996   3         3         0       32s
````
#
# DiectPV
#
```
# kubectl directpv info
┌──────────┬──────────┬───────────┬─────────┬────────┐
│ NODE     │ CAPACITY │ ALLOCATED │ VOLUMES │ DRIVES │
├──────────┼──────────┼───────────┼─────────┼────────┤
│ • node11 │ -        │ -         │ -       │ -      │
│ • node12 │ -        │ -         │ -       │ -      │
│ • node13 │ -        │ -         │ -       │ -      │
│ • node14 │ -        │ -         │ -       │ -      │
│ • node15 │ -        │ -         │ -       │ -      │
│ • node16 │ -        │ -         │ -       │ -      │
│ • node21 │ -        │ -         │ -       │ -      │
│ • node22 │ -        │ -         │ -       │ -      │
│ • node23 │ -        │ -         │ -       │ -      │
└──────────┴──────────┴───────────┴─────────┴────────┘
# kubectl directpv discover

 Discovered node 'node23' ✔
 Discovered node 'node12' ✔
 Discovered node 'node13' ✔
 Discovered node 'node15' ✔
 Discovered node 'node16' ✔
 Discovered node 'node21' ✔
 Discovered node 'node22' ✔
 Discovered node 'node11' ✔
 Discovered node 'node14' ✔

┌─────────────────────┬────────┬───────────┬──────────┬────────────────┬─────────────────────────┬───────────┬─────────────┐
│ ID                  │ NODE   │ DRIVE     │ SIZE     │ FILESYSTEM     │ MAKE                    │ AVAILABLE │ DESCRIPTION │
├─────────────────────┼────────┼───────────┼──────────┼────────────────┼─────────────────────────┼───────────┼─────────────┤
│ 179:3$kXURoSnXZS... │ node11 │ mmcblk0p3 │ 1000 MiB │ xfs            │ (Part 3)                │ YES       │ -           │
│ 259:0$S2yueNJpQA... │ node11 │ nvme0n1   │ 932 GiB  │ ceph_bluestore │ Samsung SSD 980 PRO 1TB │ YES       │ -           │
│ 179:3$f3W1yP6l4P... │ node12 │ mmcblk0p3 │ 1000 MiB │ xfs            │ (Part 3)                │ YES       │ -           │
│ 259:0$qmIF1bbaun... │ node12 │ nvme0n1   │ 932 GiB  │ ceph_bluestore │ Samsung SSD 980 PRO 1TB │ YES       │ -           │
│ 179:3$ixCm8ycERB... │ node13 │ mmcblk0p3 │ 1000 MiB │ xfs            │ (Part 3)                │ YES       │ -           │
│ 259:0$mn43JEuIWt... │ node13 │ nvme0n1   │ 932 GiB  │ ceph_bluestore │ Samsung SSD 980 PRO 1TB │ YES       │ -           │
│ 179:3$MaWGh/vWL8... │ node14 │ mmcblk0p3 │ 1000 MiB │ xfs            │ (Part 3)                │ YES       │ -           │
│ 259:0$9G5nze9Nkl... │ node14 │ nvme0n1   │ 932 GiB  │ ceph_bluestore │ Samsung SSD 980 PRO 1TB │ YES       │ -           │
│ 179:3$/0mfwAxpXU... │ node15 │ mmcblk0p3 │ 1000 MiB │ xfs            │ (Part 3)                │ YES       │ -           │
│ 259:0$gUIcbJPPJn... │ node15 │ nvme0n1   │ 932 GiB  │ ceph_bluestore │ Samsung SSD 980 PRO 1TB │ YES       │ -           │
│ 179:3$ibQZ5HrUsN... │ node16 │ mmcblk0p3 │ 1000 MiB │ xfs            │ (Part 3)                │ YES       │ -           │
│ 259:0$88ELlAGAC4... │ node16 │ nvme0n1   │ 932 GiB  │ ceph_bluestore │ Samsung SSD 980 PRO 1TB │ YES       │ -           │
│ 179:3$v1F46Q3qHy... │ node21 │ mmcblk0p3 │ 1000 MiB │ xfs            │ (Part 3)                │ YES       │ -           │
│ 259:0$vv5p5MOV/u... │ node21 │ nvme0n1   │ 932 GiB  │ ceph_bluestore │ Samsung SSD 980 PRO 1TB │ YES       │ -           │
│ 179:3$XQC2bAgcw1... │ node22 │ mmcblk0p3 │ 1000 MiB │ xfs            │ (Part 3)                │ YES       │ -           │
│ 259:0$wDqhFHZjJH... │ node22 │ nvme0n1   │ 932 GiB  │ ceph_bluestore │ Samsung SSD 980 PRO 1TB │ YES       │ -           │
│ 179:3$DOobGdpTs/... │ node23 │ mmcblk0p3 │ 1000 MiB │ xfs            │ (Part 3)                │ YES       │ -           │
│ 259:0$f8I0b8Wq4W... │ node23 │ nvme0n1   │ 932 GiB  │ ceph_bluestore │ Samsung SSD 980 PRO 1TB │ YES       │ -           │
└─────────────────────┴────────┴───────────┴──────────┴────────────────┴─────────────────────────┴───────────┴─────────────┘

Generated 'drives.yaml' successfully.
# vi drives.yaml
# kubectl directpv init drives.yaml
ERROR Initializing the drives will permanently erase existing data. Please review carefully before performing this *DANGEROUS* operation and retry this command with --dangerous flag.
# kubectl directpv init drives.yaml --dangerous

 ███████████████████████████████████████████████████████████████████████████ 100%

 Processed initialization request '58e50fa4-5344-4782-be93-7e199d677da7' for node 'node23' ✔
 Processed initialization request 'c8991dc4-1f98-47f9-9f53-d38e312e2f1d' for node 'node13' ✔
 Processed initialization request '71ab740e-7a9c-4a68-a7f4-a1a08b643856' for node 'node14' ✔
 Processed initialization request '33fd3fd3-d34e-47ff-9f76-669ccbe2abd0' for node 'node15' ✔
 Processed initialization request '6e0210f1-2744-4d4c-8803-3910d129c219' for node 'node12' ✔
 Processed initialization request 'a9118beb-b9e3-4beb-a15a-2ae7eb05a265' for node 'node21' ✔
 Processed initialization request 'b8a801ec-cada-40ca-9f4f-356dde9c3c36' for node 'node22' ✔
 Processed initialization request 'b0a08aac-5993-430f-b90b-eef27f020991' for node 'node11' ✔
 Processed initialization request 'a617ad99-4a8c-4ee3-9254-c809d670deef' for node 'node16' ✔

┌──────────────────────────────────────┬────────┬─────────┬─────────┐
│ REQUEST_ID                           │ NODE   │ DRIVE   │ MESSAGE │
├──────────────────────────────────────┼────────┼─────────┼─────────┤
│ b0a08aac-5993-430f-b90b-eef27f020991 │ node11 │ nvme0n1 │ Success │
│ 6e0210f1-2744-4d4c-8803-3910d129c219 │ node12 │ nvme0n1 │ Success │
│ c8991dc4-1f98-47f9-9f53-d38e312e2f1d │ node13 │ nvme0n1 │ Success │
│ 71ab740e-7a9c-4a68-a7f4-a1a08b643856 │ node14 │ nvme0n1 │ Success │
│ 33fd3fd3-d34e-47ff-9f76-669ccbe2abd0 │ node15 │ nvme0n1 │ Success │
│ a617ad99-4a8c-4ee3-9254-c809d670deef │ node16 │ nvme0n1 │ Success │
│ a9118beb-b9e3-4beb-a15a-2ae7eb05a265 │ node21 │ nvme0n1 │ Success │
│ b8a801ec-cada-40ca-9f4f-356dde9c3c36 │ node22 │ nvme0n1 │ Success │
│ 58e50fa4-5344-4782-be93-7e199d677da7 │ node23 │ nvme0n1 │ Success │
└──────────────────────────────────────┴────────┴─────────┴─────────┘
# kubectl directpv list drives
┌────────┬─────────┬─────────────────────────┬─────────┬─────────┬─────────┬────────┐
│ NODE   │ NAME    │ MAKE                    │ SIZE    │ FREE    │ VOLUMES │ STATUS │
├────────┼─────────┼─────────────────────────┼─────────┼─────────┼─────────┼────────┤
│ node11 │ nvme0n1 │ Samsung SSD 980 PRO 1TB │ 932 GiB │ 931 GiB │ -       │ Ready  │
│ node12 │ nvme0n1 │ Samsung SSD 980 PRO 1TB │ 932 GiB │ 931 GiB │ -       │ Ready  │
│ node13 │ nvme0n1 │ Samsung SSD 980 PRO 1TB │ 932 GiB │ 931 GiB │ -       │ Ready  │
│ node14 │ nvme0n1 │ Samsung SSD 980 PRO 1TB │ 932 GiB │ 931 GiB │ -       │ Ready  │
│ node15 │ nvme0n1 │ Samsung SSD 980 PRO 1TB │ 932 GiB │ 931 GiB │ -       │ Ready  │
│ node16 │ nvme0n1 │ Samsung SSD 980 PRO 1TB │ 932 GiB │ 931 GiB │ -       │ Ready  │
│ node21 │ nvme0n1 │ Samsung SSD 980 PRO 1TB │ 932 GiB │ 931 GiB │ -       │ Ready  │
│ node22 │ nvme0n1 │ Samsung SSD 980 PRO 1TB │ 932 GiB │ 931 GiB │ -       │ Ready  │
│ node23 │ nvme0n1 │ Samsung SSD 980 PRO 1TB │ 932 GiB │ 931 GiB │ -       │ Ready  │
└────────┴─────────┴─────────────────────────┴─────────┴─────────┴─────────┴────────┘
```
