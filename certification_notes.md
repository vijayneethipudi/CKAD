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