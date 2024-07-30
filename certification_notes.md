# CKAD Certification Notes
**_NOTE:_** If a resource ends with 's', then the following will starts with '- name' means it accepts a list of sub-resources.
## Volumes
**_NOTE:_**  *volumes* is a pod spec while the *volumeMounts* is container spec
### hostPath
Data is stored in a specific location directly on the host file system, on the Kubernetes node where the Pod is running. 
#### Supported Types
* **File** - path is a file, if the file doesn't exist then the pod creation will fail.
* **FileOrCreate** - path is a file, if the file doesn't exist then it creates one.
* **Directory** - path is a directory, if the directory doesn't exist then the pod creation will fail.
* **DirectoryOrCreate** - path is a directory, if the directory doesn't exist then it creates one.
### emptyDir
Data is stored in an automatically managed location on the host file system. Data is delete if the Pod is deleted.
### persistentVolumes (PV) and PersistentVolumeClaim (PVC)
A PersistentVolume allows you to abstract volume storage details away from Pods and treat storage like a consumable resource.
* PersistentVolume 
    * Defines an abstract storage resource ready to be consumed by Pods.
    * Defines details about the **type** and **amount** of storage provided.
* PersistentVolumeClaim
    * Defines a request for storage, including details on the **type of storage needed**.
    * Automatically binds to an available PersistentVolume that meets the provided requirements.
    * Mounted in a Pod like any volume
#### Exam Tips
* The **volumes** field in the Pod spec defines the details about the volumes used in the Pod. 
* The **volueMounts** field in the container spec mounts a volume to a specific container at a specific location.
* **hostPath** volumes mount data from a specific location on the host (k8s node).
* **hostPath** contains a type field 
    * Directory - Mounts an existing directory on the host.
    * DirectoryOrCreate - Mounts a directory on the host, and creates it if it doesn't exist.
    * File - Mounts an existing single file on the host.
    * FileOrCreate - Mounts a file on the host, creates it if doesn't exist.
* **emptyDir** volumes provide ephemeral (temporary) storage that uses the host file system and are removed if the Pod is deleted. 
* **PersistentVolume** defines a storage resource.
* **PersistentVolumeClaim** defines a request to consume a storage resource.
* **PersistentVolumeClaims** automatically bind to a PersistentVolume that meets the criteria. 
* Mount a PersistentVolumeClaim to a container like a regular volume.

## Deployments
A deployment actively manages a desired state for a set of replica Pods.
### Performing Rolling Updates
A rolling update allows you to change a Deployments Pod template, gradually replacing replicas with zero downtime.

You can use rolling updates to deploy **new code!**.
* It can be done using rollout command as well as simply modifying the image on the manifest file using either kubectl edit command or kubectl apply -f command. 


### Commands
To scale we can use below methods
1. Running kubectl scale command on the deployment
```
kubectl scale deployment/nginx-deployment --replicas=4
```
2. Modifying the replicas file directly using "kubectl edit"
```
kubectl edit deployment nginx-deployment
```
3. Modifying the template file and then running apply command
```
kubectl apply -f deployment.yaml
```
#### Exam Tips
* A Deployment actively manages a desired state for a set of replica pods.
* The Pod template provies the Pod configuration that the Deployment will use to create new Pods. 
* The replicas field sets the number of replicas. You can set it Up or Down to scale up or scale down. 
* A rolling update gradually rolls out changes to a Deployment's Pod template by gradually replacing replicas with the new ones. 
* Use **Kubectl rollout status** to check the rolling update.
* We can undo the rollout update using **kubectl rollout undo**
## Deployment Strategy
### Blue/Green deployment strategy
* It involves using 2 identical production environments, usually called blue and green. 
* New code is rolled out to the second environment. This environment is confirmed to be stable and working before user traffic is directed to the new environment.
### Canary Strategy
* Just like blue/green, a canary deployment strategy uses 2 new environment.
* A portion of the user base is directed to th enew code in order to expose any issues before the changes are rolled out to everyone else.
## Probe
Allows the user to put health checks on a container.
### Types of Probes
#### Liveness
* Liveness probe check whether a container is healthy so that it can be restarted if it becomes unhealthy.
#### Readiness
* Readiness probe determines when a container is fully started up and ready to receive user traffic. 
#### Startup
* Startup probe check container health during startup for slow-starting containers. 
### Probe uses 3 types of checks
* command
* httpGet
* TCP
## Monitoring
It is the process of gathering data(metrics) about the performance of your containerized applications.
### Steps to Access Data with the Metrics API
* *Install Metric Server*: This is a component that gathers the metric data, so that we can access it. Without Metric server, the metrics API will not provide any data.
* *Access Metric Data*: The kubectl top command can be used to view metric cata once it is gathered by *Metric Server*
#### Exam Tips
* You can view resource usage using ```kubectl top``` command
### Container logs
* Kubernetes stores stdout/stderr console output for each container in the container log
* You can view the logs using ```kubectl logs```
### Debugging Kubernetes
* To fetch all namespaces pods
    ```kubectl get pods --all-namespaces```
* Describe is another useful command to look a certain pod
    ```kubectl describe pod <<podname>>```
## Application Environment, Configuration, and Security
### Using Custom Resources (CRD)
* A CustomResourceDefinition (CRD) defines a custom resource.
* You can create *custom controller* that act upon custom resources.
* *Note:* Custom controllers are not part of the CKAD curriculum.
#### Exam Tips
* Custom resources are extensions of the kubernetes API.
* A CustomResourceDefinition defines a custom resource
### Service Accounts
* if a process within a container to authenticate with the
kubernetes api server then you can use ServiceAccount to do that.
* Service Account can be assigned permissions via role-based access
control, just like regular user accounts.
* Service Account Token is the credential used to access the API
using the ServiceAccount.
* The token for a pod's service account is automatically mounted to each container.
* We need to mount the serviceAccountName on the Pods
    * it will automount the service account token on the below path in each container
        * /var/run/secrets/kubernetes.io/serviceaccount/token
* Service account is for Authentication purpose; you also need to make sure it has access.
#### Exam Tips
* ServiceAccounts allow process within containers to authenticate with the kubernetes API Server
* You can set the Pod's ServiceAccount with *serviceAccountName* in the pod spec
* The Pod's ServiceAccount token is automatically mounted to the Pod's creation

### Users vs Service Accounts
| Users | Service Accounts |
| ---- | ---- |
| Not represented by a kubernetes object | Represented by a kubernetes object |
| Usually authenticates using a client certificate | Usually authenticates using an API Token |
| Usually used by human users or tools running outside the cluster | Usually used by automated processes running within containers |
| | |

### Role-Based Access Control (RBAC)
* Manage authorization in kubernetes
### Admission Controller
* It intercepts requests to the Kube API after authentication and authorization, but before any objects are persisted. They can be used to validate, deny or even modify the request.
* For example, you are creating a pod with a namespace that is not available, when you apply it will fail.
    * But you can add a plugin to the adminssion controller that auto provision the namespace, then it automatically creates the namespace without giving any errors.
    * ```sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml``` -> This is the file where you can add the plugins to the admission controller
        * ``` --enable-admission-plugins=NodeRestriction,NamespaceAutoProvision,ResourceQuota```
    * Once you modify the file, the k8s API Server will take sometime to restart, so wait until it restarts
### Manage Compute Resource Usage
| Requests | Limits |
| ---- | ---- |
| Provies k8s with an idea of how many/much resources a container is expected to use | Provides an upper limit on how many/much resources a container is allowed to use |
| The cluster uses this information to select a node that has enough resources available | The cluster uses this information to terminate the container process it it attempts to use more than the allowed amount |
| | |

#### Resource Quota
* A ResourceQuota is a k8s object taht sets limits on the resources used within a namespace.
* If creating or modifying, a resource would go beyond the limits the request will be denined.
### ConfigMaps and Secrets
| ConfigMap | Secrets |
| ----- | ----- |
| It is an object that stores configuration data for applications in the form of key-value pairs. This data can be passed to containers at runtime | Secret is similar to ConfigMap. It stores data that can be passed to containers, but it is designed to store sensitive data such as passwords or API tokens |
| | |

#### Volume Mounts vs Environment Variables
There are two ways to pass ConfigMap and Secrets data to containers
| VolumeMounts | Environment Variables |
| ------- | ------- |
| Data appears in the containers file system at runtime | Data appears in the environment variables visible to the container at runtime |
| Each top-level key in the config data becomes a file name | You can specify keys and variables names for each piece of data |
| | |

## Configuring SecurityContext
### SecurityContext
A security context provides advanced security features to a pod or container
#### UserId and GroupId
You can customize the UserId (UID) and/or GroupId (GID) used by container space
#### Allow Privilege Escalation
You can enable or disable privilege escalation for the container process
#### Read-Only Root Filesystem
Youc an set the containers root filesystem to read-only
#### Exam Tips
* Allows you to control advanced security related settings for the container
* Set the container's UserId (UID) and/or GroupId (GID) with *securityContext.runAsUser* and *securityContext.runAsGroup*
* Enable or Disable privilege escalation with *securityContext.allowPrivilegeEscalation*
* Make the container root filesystem read-only with *securityContext.readOnlyFilesystem* -> for example you logged into the container with **exec --** command and if you try to create a file in the root filesystem it will throw an error
## Services and Networking
### Network Policies
Kubernetes cluster uses virtual network which allows Pods to communicate with each another seamlessly even if they are on different nodes
* A NetworkPolicy is a k8s object that allows you to restrict network traffic to and from Pods within the cluster network
* Use NetworkPolicies to **block** unnecessary or unexpected network traffic and make your applications more secure

| Non-Isolated Pods | Isolated Pods |
| ----- | ------ |
| Any Pod that is not selected by any network policy | Any Pod that is selected by at-least 1 NetworkPolicy |
| Open to all incoming and outgoing network traffic (NetworkPolicies don't apply) | Only open to traffic that is explicitly allowed by a NetworkPolicy that selects the Pod
| | |

### Services
A Service allows you to expose an application running accross multiple Pods to the Network

Clients can communicate with the Service, and traffic will be automatically routed to one of the underlying Pods
#### Types of Services
* ClusterIP Service - Exposes the application within the cluster network where it can be accessed by other Pods.
* NodePort - exposes the Application externally by listening on an external Port on each cluster Node
#### Ingress
K8s object that manages access to Service from outside the cluster. For example, you can use Ingres to provide your end users with access to your web applications running in k8s
* Ingress routes traffic to Service which routes the traffic to Pods
* **Note:** You need *ingress controller* to actually implement the Ingress functionality. Without ingress controller, we can still create the ingress objects but it won't work.
