# Practices and Learnings for Docker and Kubernetes

## Docker

### Image

Image is the read-only template that tells us how the container will be realized.

### Container

1. Container is the runtime instance of an image that gets created.
2. Containers are ephemerous and stateless. Don't store data here.


### Container Registries

1. Central respositories for container images.
2. Private/Public.
3. Docker Hub, Microsoft (Azure Container Registry), Amazon Elastic Container Registry, Google Container Registry.


### Commands

> docker images

> docker stop xxxx

> docker rm xxxx

> docker build ....

> docker run ....

### Docker Compose

1. Define and run multi-container applications.
2. Define using YAML files.
3. Run using the docker CLI with the compose plugin `Docker compose`.
4. Set Resource Limits (e.g. CPU, Memory), Environment Variables, and Restart Policy.
5. Use for Networking, Dependencies.

    #### Use Cases
    1. Workloads that don't require a full orchestrator.
    2. Development and tests locally.
    3. Use of a service that can run Docker-compose files.

## YAML

1. Human friendly data serialization standard.
2. Used by Docker-Compose and Kubernetes (Copy from [kubernetes.io/docs](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)).
3. A superset of JSON.


## Kubernetes

1. Donated by Google to the Cloud Native Computing Foundation([CNCF](https://www.cncf.io/)).
2. Designed as a loosely coupled collection of components centered around deploying, maintaining and scaling workloads.
3. Vendor-neutral.
4. Backed by a huge community.

    ### Use Cases
    1. Service discovery and load balancing.
    2. Storage orcestration (Local or Cloud based).
    3. Automated rollouts and rollbacks.
    4. Self-healing.
    5. Seret and configuration management.
    6. Use the same API across on-premise and every cloud providers.

    ### Architecture
    1. Container runs in a Pod.
    2. Pod runs in a Node. A pod can run many containers. One of the containers is the main worker, with application logic.
    3. Node runs in a Cluster. A node can run many pods.
    4. Master - Control Plane : location of the services and controller. We do NOT run containers in this master node!
    5. etcd : key value datastore where the state of the cluster is stored. <br>
        a. Single source of truth. <br>
        b. NOT a database or a datastore for applications to use.
    6. kube-apiserver : the only component that communicates with etcd. <br>
        a. Exposes a REST interface and client tools. <br>
        b. Saves state to the datastore (etcd). <br>
        c. All clients interact with this, NEVER directly to the datastore.
    7. kube-control-manager : the controller of controllers.
    8. cloud-control-manager : interacts with the cloud providers controlers.
    9. kube-scheduler : watches newly created pods that have no node assigned, and selects a node for them to run on.
    10. Worker Nodes have : <br>
        a. kubelet : manage the pods lifecycle. <br>
        b. kube-proxy : networy proxy that manages network rules on nodes. <br>
        c. container runtime : must implement the kubernets container runtime interface.
    11. Pods <br>
        a. Atomic unit of the smallest unit work of K8s.<br>
        b. Encapsulates an application's container.<br>
        c. Represents a unit of deployment.<br>
        d. Can run one or multiple containers.<br>
        e. Containers within a pod share IP address space, mounted volumes, and communicate via Localhost, IPC.<br>
        f. Ephemeral (short lived).<br>
        g. Deploying a pod is an atomic operation.<br>
        h. If it fails, it is repalced with a new one with a shiny new IP address.<br>
        i. We don't update a pod, we replace it with an updated version.<br>
        j. We scale by adding more pods, not more containers in the pod.


    ### CLI
    1. `kubectl` communicates with the apiserver.
    2. Configuration stored locally. <br>
        a. ${HOME}/.kube/config <br>
        b. C:\Users\ {USER}\ .kube\config.
    
    ### Context
    1. A context is a group of access parameters to a K8s cluster.
    2. Contains a kubernetes cluster, a user, and a namespace.
    3. The current context is the cluster that is currently the default for kubectl. <br>
        a. All kubectl commands run against that cluster. <br>
     