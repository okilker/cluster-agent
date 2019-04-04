# AppDynamics ClusterAgent

AppDynamics ClusterAgent is an application for monitoring workloads on Kubernetes clusters. It is implemented in Golang as a native Kubernetes component. The ClusterAgent is designed to work with AppDynamics controller and is associated with a specific AppDynamics tenant. 
The ClusterAgent has 2 purposes. 
 * It collects metrics and state of Kubernetes resources and reports them to an AppDynamics controller.
 * It instruments AppDynamics application agents into workloads deployed to the Kuberenetes cluster.


## Cluster monitoring
The ClusterAgent monitors state of several Kuberenetes resources and derives metrics to provide visibility into the following common application impacting issues:

* Application crashes
* Misconfiguration
* Missing dependencies
* Missing connectivity
* Resource starvation and overutilization
* Image issues
* Storage issues
* Resource utilization relative to capacity and limits

The metrics are pushed to the AppDynamics controller under the application name and the tier of the ClusterAgent. In addition, the raw snapshot data is sent to the Controller as Analytics events and can be viewed and further analyzed with ADQL.
A cluster-level dashboard with metrics is generated out-of-the-box. Deployment specific dashboards can be generated on demand, by changing the ClusterAgent configuration


## Application instrumentation
The ClusterAgent uses a declarative approach to agent instrumentation, which is consistent with Kubernetes design principles. The agent instrumentation is initiated by changong the deployment spec of the apps that need to be monitored. The ClusterAgent adds an init container with the desired agent inage to the deployment. The initcontainer c the agent binaries to a shared volume on the pod and make them available to the main application container. The required agent parameters are passed to the main application container as environment variables. 
In addition to this method, some Java workloads can be also instrumented using Java dynamic attach.
Once an application is instrumented, the ClusterAgent associates the pod with the AppDynamics application/tier/node ids. For Java workloads, the association is implemented down to the node id. For other technologies, the association is at the app/tier level. The ids of the corresponding AppDynamics entities are reflected in the pod's annotations.
By default, the instrumentation is disabled. The instrumentation is controlled by several configuration settings.
InstrumentationMethod "none", "mountEnv", "mountAttach"
NSToInstrument
NSToInstrumentExclude
NSInstrumentRule

To enable instrumentation, the InstrumentationMethod must be either mountEnv or mountAttach and NSToInstrument must have at least 1 namespace.

The instrumentation can be declared at a deployment level or via ClusterAgent configuration. The ClusterAgent makes the instrumentation decision in this order:
Is it enabled: InstrumentationMethod is not "none" and the deployment namespace is not excluded
Is there a deployment metadata
Is there a rule that matches the deployment
Is there a namespace-wide rule that matches the deployment

### Deployment metadata

`appd-app: marvel
 appd-agent: dotnet`


### ClusterAgent configuration
* Global defaults
* Specific rule
	* Tech, multiple namespaces, first container
	* Tech, multiple namespaces, specific container
	* Tech, multiple namespaces, multiple containers
	* Tech, multiple namespaces, specific container, method override
* Namespace-wide rule



## Quick start
The ClusterAgent can be deployed and managed manually or with a Kuberenetes Operator. The Kubernetes Operator is a recommended approach, as it hides a number of steps and compexities.
Update controller URL in the configMap
Create an AppDynamics account
Create a Secret
Deploy the folder
`kubectl create -f deploy/`

### Manual deployment
Create a Secret with at least one key:
"api-user"

The ClusterAgent is designed to listen to updates to its configMap and use the new values of most of the settings  without restart.

The default ConfigMap is attached.
 ControllerUrl is the only required value

### ClusterAgent Operator
Link

