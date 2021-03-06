# Zookeeper helm chart
The simple helm chart bases on an implementation of the [ZooKeeper StatefulSet](https://github.com/helm/charts/tree/master/incubator/zookeeper) in [kubernetes/contrib](https://github.com/kubernetes/contrib) repo.

## Prerequisites
To use the charts here, [Helm](https://helm.sh/) must be installed in your
Kubernetes cluster. Setting up Kubernetes and Helm and is outside the scope
of this README. Please refer to the Kubernetes and Helm documentation.
The versions required are:
  * **Helm 2.12+** - This is the earliest version of Helm tested. It is possible
    it works with earlier versions but this chart is untested for those versions.
  * **Kubernetes 1.11+**  with Beta APIs enabled. This is the earliest version of Kubernetes tested.
    It is possible that this chart works with earlier versions but it is
    untested.
  * **Persistence volume** support on underlying infrastructure
  * **NetworkPolicy** should be enabled on a Kubernetes cluster in case if network accessibility of Zookeeper containers inside of the cluster, must be restricted by enabling `cluster.networkPolicy` chart parameter.

## Installing the Chart
To use the charts, you must download this repository or the chart archive file and unpack it into a directory. Assuming this repository was unpacked into the directory `zookeeper-helm`, the chart can then be installed directly:
```console
$ wget https://github.com/frizner/zookeeper-helm/archive/v0.1.0.tar.gz 
$ tar -zxf v0.1.0.tar.gz  
$ helm install --name zkcluster1 ./zookeeper-helm-0.1.0
```
As result helm should deploy realese `zkcluster1` using default configuration. 
The [configuration](#configuration) section lists the parameters that can be configured during the installation.

## Uninstalling the Chart

To uninstall/delete the chart/`zkcluster1` deployment:

```console
$ helm delete --purge zkcluster1
```
The command removes all the Kubernetes components associated with the release, excluding `PersistentVolumeClaim` resources, and deletes the release.

## Cleanup orphaned Persistent Volumes

Deleting a StateFul will not delete associated Persistent Volumes.

Do the following after deleting the chart release to clean up orphaned Persistent Volumes.

```bash
$ kubectl delete pvc -l zkcluster=zkcluster1
```

## Configuration

The following table lists some of the configurable parameters of the Zookeeper chart and their default values.

|Parameter|Description|Default
---:|:---:|:---
`size`|Number of ZooKeeper nodes in the cluster|3
`podDisruptionBudget.maxUnavailable`|The number of nodes that can be unavailable|1
`updateStrategy.type`|This feature can be used to upgrade the container images, resource requests and/or limits, labels, and annotations|RollingUpdate
`image`| Docker image | `k8s.gcr.io/kubernetes-zookeeper:1.0-3.4.10` 
`maxUnavailable`|Number of unavailable ZooKeeper nodes in the cluster|1
`imagePullPolicy`|Zookeeper image pull policy|IfNotPresent
`networkPolicy`|If set to `enabled` restricts network access to server and election ports of each node by nodes of the same Zookeeper cluster. `NetworkPolicy` should be enabled on a Kubernetes cluster|disabled
`resources`|CPU/Memory resource requests/limits|Requests: 0.5/1Gi, Limits: 1/2Gi, see [values.yaml](values.yaml)
`livenessProbe`|A command and parameters for livenessProbe test|see [values.yaml](values.yaml)
`readinessProbe`|A command and parameters for readinessProbe test|see [values.yaml](values.yaml)
`heap`|Size of the Java Heap|512M
`storage`|Size of a persist volume for the each Zookeeper node|10Gi
`logLevel`|Level of logging|INFO
`clientPort`|Client port|2181
`serverPort`|Server follower port|2888
`leadElectionPort`|Leader election port|3888
`user`|User ID to start Zookeeper process|1000
`group`|Group ID to start Zookeeper process|1000
`initLimit`|Amount of time, in ticks, to allow followers to connect and sync to a leader.|1
`tickTime`|The length of a single tick, milliseconds|2000
`maxClientCnxns`|Limits the number of concurrent connections (at the socket level) that a single client, identified by IP address, may make to a single member of the ZooKeeper ensemble.|60
`snapRetainCount`|When enabled, ZooKeeper auto purge feature retains the autopurge.snapRetainCount most recent snapshots and the corresponding transaction logs in the dataDir and dataLogDir respectively and deletes the rest. |3
`purgeInterval`|The time interval in hours for which the purge task has to be triggered.|12
`maxSessionTimeout`|The maximum session timeout in milliseconds that the server will allow the client to negotiate.|40000
`minSessionTimeout`:|The minimum session timeout in milliseconds that the server will allow the client to negotiate.| 4000

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
$ helm install --name zkcluster1 -f values.yaml ./zookeeper-helm
```
> **Tip**: You can use the default [values.yaml](values.yaml)


