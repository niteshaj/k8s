###Basic Kubernetes objects include:
- Pod: A Pod is the basic building block of Kubernetes. The smallest and simplest unit in the Kubernetes object model that 
    you create or deploy. A Pod encapsulates an application’s container (or, in some cases, multiple containers), 
    storage resources, a unique network IP, and options that govern how the container(s) should run. A Pod represents a 
    unit of deployment. 
    > Docker is the most common container runtime used in a Kubernetes Pod, but Pods support other container runtimes as well.
   
    > pod share same IP, localhost, IPC
- Service: A Kubernetes Service is an abstraction which defines a logical set of Pods and a policy by which to access them - 
    sometimes called a micro-service. The set of Pods targeted by a Service is determined by a Label Selector (The label 
    selector is the core grouping primitive in Kubernetes. Via a label selector, the client/user can identify a set of objects)
    
    K8S use two type of communication using service
    1. Cluster ip: A type of service which is only accessible within a Kubernetes cluster, or the internal ("virtual") IP of components within a Kubernetes cluster. 
    2. Nodeport
	we define the type as NodePort (as we want to make the frontend available to outside of the cluster). The backend only hasto be reachable from within the cluster, therefore, the type was ClusterIP.
    
- Volume
- Namespace

##Kubernetes contains a number of higher-level abstractions called Controllers. Controllers build upon the basic objects, and provide additional functionality and convenience features
- ReplicaSet
- Deployment
- StatefulSet
- DaemonSet
- Job





Pods
A pod is the basic unit that Kubernetes deals with, a group of containers. If there are two or more containers that always need to work together, and should be on the same machine, make them a pod.

Node
A node is a physical or virtual machine, running Kubernetes, onto which pods can be scheduled.

Label
A label is a key/value pair that is used to identify a resource. You could label all your pods serving production traffic with “role=production”, for example.

Selector
Selections let you search/filter resources by labels. Following on from the previous example, to get all production pods your selector would be “role=production”.

Service
A service defines a set of pods (typically selected by a “selector”) and a means by which to access them, such as single stable IP address and corresponding DNS name.