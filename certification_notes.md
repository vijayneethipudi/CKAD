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