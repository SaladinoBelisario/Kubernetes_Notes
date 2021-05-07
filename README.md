# **Kubernetes_Notes**


## What is Kubernetes?
Kubernetes is an orchestrator for containers like Swarm, runs on top of Docker
usually as a set of APIs in containers.

Provides API or CLI to manage container across servers.

Many clouds provide it for you.

Many vendors make a "distribution" of it.

### System parts
* Kubernetes: The whole orchestratuion system
* Kubectl: CLI to manage Kubernetes and manage apps.
* Node: Single server in the Kubernetes cluster.
* Kubelet: Kubernetes agent running on nodes.
* Control Plane: Set of containers that manage the cluster.

### Pods
Basic unit of deployment, containers are always in pods.

Controller: For creating/updating pods and other objects.

Service: Endpoint to connect to a pod.

Namespace: Filtered group of objects in cluster.

### Creating pods with kubectl
First, be sure kubernetes its running:
> kubectl version

Two ways to deploy Pods(containers): via CLI or via YAML.

Example for the CLI:
> kubectl run my-nginx --image nginx

Starting in **version 1.18** (released March 2020), the kubectl run command 
only does one thing: **create single pods**. There were many reasons for this, 
but the big ones were to reduce the complexity of how the run command 
worked and to move other resource creation to the kubectl create command.
The idea is that kubectl run is now similar to docker run. It creates a
single pod, where docker run creates a single container.

### Scaling a ReplicaSet
Example of scaling an existing Apache server replica:
> kubectl scale deploy/my-apache --replicas 2
> 
> or
> 
> kubectl scale deployment my-apache --replicas 2

### Inspecting Deployment Objects
Showing your pods:
> kubectl get pods

Example of showing the logs of the Apache Pod:
> kubectl logs deployment/my-apache --follow --tail 1

## Exposing Kubernetes ports
* _kubectl service_ creates a service for existing pods.
* A service is a stable address for pod(s).
* If we want to connect with pods we need services.
* CoreDNS allow us to resolve services by name.
* There are different type of services.

### Basic Service Types:
* ClusterIP (default):
  - Single, internal virtual IP allocated.
  - Only reachable within the cluster (nodes and pods).
  - Pods can reach service on apps port number.
* NodePort:
    - High port allocated on each node.
    - Port it's open on every node's IP. 
    - Anyone can connect (if they can reach node).
    - Other pods need to be updated to this port.
    - These services are always available in Kubernetes.
* Load Balancer:
    - Controls a LB endpoint external to the cluster.
    - Only available when infra provider gives you a LB (ej. AWS ELB).
    - Creates NodePort+ClusterIP services, tells LB to send to NodePort.
* ExternalName:
    - Adds CNAME DNS record to CoreDNS only.
    - Not used for Pods, but for giving pods a DNS name to use for something 
      outside Kubernetes.
    - Anyone can connect (if they can reach node).
  
## Management techniques

### Run, Create and expose Generators
* Every resource in Kubernetes has a specification.
> kubectl create deployment sample --image nginx --dry-run -o yaml


## Declarative Kubernetes

You can use _kubctl apply_ to use a YAML file with a declarative approach, this it's a 
more complex format than Compose, but provides an improved flexibility and 
extensibility.

* Each file contains one or more manifests.
* Each manifest describes an API object(deployment, job, secret).
* Each manifest requires four parts (root key:values in the file):
  - apiVersion: 
  - kind:
  - metadata:
  - spec:
  
For obtaining a detailed explanation for all keys each kind supports:
> kubectl explain services.spec

_spec:_ can have sub _spec:_ for other services.
> kubectl explain deployment.spec.template.spec.volumes.nfs.server

we can also use the docs.