## Cluster monitoring

The ClusterAgent monitors state of Kuberenetes resources and derives metrics to provide visibility into common application impacting issues. The metrics are displayed in the cluster overview dashboard, which is generated by the ClusterAgent application out-of-the-box.

![Cluster Overview Dashboard](https://github.com/Appdynamics/cluster-agent/blob/master/docs/assets/cluster-dashboard.png)


### Metrics

The ClusterAgent is a golang application which is instrumented with AppDynamics Golang SDK and monitored by AppDynamics.
The metrics that the ClusterAgent collects are sent to the AppDynamics controller via REST API and are stored under the application name and the tier of the ClusterAgent. 

The metrics collected are grouped in the following categories:

* Application crashes and restarts
* Resource starvation and overutilization (nodes under memory or disk pressure and a number of early indicators of evictions)
* Image issues (image pull failures and misconfigurations)
* Storage issues (failed bindings, quota viloations)
* Resource utilization relative to capacity and limits
* Missing dependencies (pods missing configMaps or Secrets)
* Missing connectivity (pods with exposed ports without services)

For example, once a Kuberenetes event is received, the ClusterAgent associates it with a catgory and a sub-category.

Categories:

* Info
* Error

Sub-categories:

* Pod
* Image
* Storage
* Quota
* Eviction

Eviction threats are derived from node conditions (e.g. disk and memory pressure) and the following events:

* InsufficientFreeCPU
* InsufficientFreeMemory
* FailedDaemonPod
* NodeHasDiskPressure
* NodeHasMemoryPressure
* EvictionThresholdMet
* ErrorReconciliationRetryTimeout

The Cluster Overview Dashboard displays the following metrics (from left to right, top to bottom)

- Error events
- Number of evicted pods
- Events signaling eviction treats
- Number of pending pods
- Average pending time of pods
- Number of pod restarts
- Used Cpu vs total cluster capacity
- Used Memory vs total cluster capacity
- Number of pods vs total number of pods allowed per kublet configuration
- Number of master nodes. On managed environments (e.g. EKS) this number will show 0.
- Number of worker nodes
- % nodes under disk pressure
- % nodes under memory pressure

- Pod crashes and restarts
- Image pull errors and pulling
- Storage bind failures and quota violations

- Number of pods
- % of privileged pods
- % of pods with no resource limits defined
- % of pods with no health probes defined (readiness, liveness)
- Pie chart of pod phases
- Missing dependencies (configMaps + Secrets, Services)
- Reduction in the number of replicas (scale-downs, pod kills)

- Cpu utilization of pods (used, requests and limits)
- Memory utilization of pods (used, requests and limits)
- Utilization of persistent volume claims (claimed vs capacity)

- Number of namespaces
- Number of jobs
- Job status (failed vs other)
- Number of deployments
- Number of replica sets
- Number of daemon sets

- % Pods without quotas
- % used Cpu Limits quotas
- % used Cpu Requests quotas
- % used Memory Limits quotas 
- % used Memory Requests quotas 
- % used Pod quotas
- % used PVC quotas
- % used Storage quotas
- % used Ephemeral Storage quotas

- Number of Services
- Number of Service Endpoints
- % Endpoints with not ready IPs
- % Orphan Endpoints (no IPs)

- Heat map of deployed pods. 

The heat map displays the status of all pods deployed to the monitored namespaces of the cluster. 
The pods are depicted as color-coded squares:

* Running pods
* Pending pods
* Failed pods
* Evicted pods
* Pods monitored by AppDynamics agents
* Pods that are over-consuming cpu or memory
* Pods that are restarting

If a pod has more than 1 container, the number of containers is displayed. 
The dashboard also provides information about each pod, when hovering the cursor over the pod square.
For all pods the information includes the pod name, namespace and the name of the node, where the pod is deployed, and the current cpu and memory utilization stats. The utilization is expressed as % of used relative to the respective requests or limits, if defined. If the limits are not defined, the value is derived from the capacity of the node, where the pod is deployed.

Additional information is provided depending on the state of the pod.
For pending pods, the pending time is shown
For restarting pods, the number of restarts is provided

The data is collected only for the namespaces and nodes that are included in monitoring per the ClusterAgent configuration. If no namespaces or nodes are specified, the entire cluster is monitored. The values of the collected metrics at any given moment in time depend on the configured monitoring scope.


### Snapshots

In addition, the ClusterAgent also collects snapshots of Kuberenetes resources and sends them to the Analytics Engine in the form of Analytics events. This data can be viewed and further analyzed in AppDynamics using various query tools, including ADQL. 

The snapshots are collected for the following entities:

* Pods
* Pod logs (for containers that crash and are being restarted)
* Deployments
* Daemon sets
* Replica sets
* Containers
* Service endpoints
* Events
* Jobs
* Resource Quotas
* Namespaces






