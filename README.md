# Practices and Learnings for Docker and Kubernetes

## Acknowledgement

Exercises and notes were referenced from [here](https://github.com/K8sAcademy/Fundamentals-HandsOn).

## Docker

### Image

1. Image is the read-only template that tells us how the container will be realized.

### Container

1. Container is the runtime instance of an image that gets created.

2. Containers are ephemerous and stateless. Don't store data here. (refer to StatefulSet to deal with this, however, a better approach could be to use the Cloud provider database services)


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
    5. etcd : key value datastore where the state of the cluster is stored. 
        a. Single source of truth. 
        b. NOT a database or a datastore for applications to use.
    6. kube-apiserver : the only component that communicates with `etcd`. 

        a. Exposes a REST interface and client tools. 

        b. Saves state to the datastore (etcd). 

        c. All clients interact with this, **NEVER** directly to the datastore.
    7. kube-control-manager : the controller of controllers.
    8. cloud-control-manager : interacts with the cloud providers controlers.
    9. kube-scheduler : watches newly created pods that have no node assigned, and selects a node for them to run on.
    10. Worker Nodes have : 

        a. kubelet : manage the pods lifecycle. 

        b. kube-proxy : networy proxy that manages network rules on nodes. 

        c. container runtime : must implement the kubernets container runtime interface.

    11. Pods 

        a. Atomic unit of the smallest unit work of K8s.

        b. Encapsulates an application's container.

        c. Represents a unit of deployment.

        d. Can run one or multiple containers.

        e. Containers within a pod share IP address space, mounted volumes, and communicate via Localhost, IPC.

        f. Ephemeral (short lived).

        g. Deploying a pod is an atomic operation.

        h. If it fails, it is replaced with a new one with a shiny new IP address.

        i. We don't update a pod, we replace it with an updated version.

        j. We scale by adding more pods, not more containers in the pod.


    ### CLI
    1. `kubectl` communicates with the apiserver.

    2. Configuration stored locally. 

        a. ${HOME}/.kube/config 

        b. C:\Users\ {USER}\ .kube\config.
    
    ### Context
    1. A context is a group of access parameters to a K8s cluster.
    2. Contains a kubernetes cluster, a user, and a namespace.
    3. The current context is the cluster that is currently the default for kubectl. 

        a. All `kubectl` commands run against that cluster. 
     
     ### Workloads
     1. A workload is an application running on kubernetes.
     2. A Pod is the atomic workload.
     3. StatefulSet

        a. Designed for Pods that must persist or maintain state. 

        b. Unlike a Deployment, a StatefulSet maintains a sticky identity for each of their Pods.

        c. Each has a *persistent* identifier (name-x)

        d. If a pod dies, it is replaced with another one using the *same* identifier.

        e. Creates a series of pods in sequence from 0 to X and deletes them from X to 0. 

        f. Typically used in stable, unique network identifiers and stable, databases using persistent storage. 

        g. Deleting a StatefulSet will not delete the PVCs (PersistentVolume Claims), it has to be done manually. 

     4. DaemonSet 

        a. Specialised workload that ensures all Nodes (or a subset) run an instance of a Pod. 

        b. Scheduled by the scheduler controller and run by the daemon controller. 

        c. Typically used in running a logs collection daemon on every node or running a node monitoring daemon on every node. 

     5. ReplicaSets 

        a. Primary method of managing pod replicas and their lifecycle to provide self-healing capabilities.

        b. Their job is to always ensure the desired number of pods are running.

        c. Provides self-healing and scalability functionality. 

        d. While we can create ReplicaSets, the recommended way is to create Deployments.

     6. Deployments 

        a. A deployment manages a single pod template.

        b. A deployment for each microservice.

        c. Deployments create ReplicaSets in the background.

        d. We don't interact with the ReplicaSets directly. 

        e. Provides rolling updates (cycle through updating pods) and rollbacks functionality. 

     7. Jobs and CronJobs

     ### Services
     1. Purpose of the services is to make the calls between pods more robust.
     2. A service is another type of K8s object.
     3. Pod IPs are unreliable but service IPs are.
     4. Durable (unlike pods)

        a. Static IP address. 

        b. Static DNS name. 
        c.[servicename].[namespace].svc.cluster.local 

    5. Services are ways to access pods.
    6. Target pods using Selectors.

    #### ClusterIP
    1. Default service in kubernetes.
    2. Visibility
        a. CLuster internal
    3. Port
        a. The port the service is listening to.

    4. TargetPort

        a. Redirecting to the port the pod(s) are listening to. (Route the incoming traffic to this port)

    5. Load balanced using round robin.

        a. Session affinity is configurable.

    6. When to use

        a. To provide a durable way to communicate with pods inside the cluster.
    
    7. With multiple microservices, we place the ClusterIP in front of each of them.


    #### NodePort

    1. NodePort extends the ClusterIP service.
    2. Visibility

        a. Internal and external.

    3. NodePort

        a. The port the service is listening to externally.

        b. Statically defined, or dynamically taken from a range between 30000-32767.

    4. Port

        a. The port the service is listening to internally.

    5. Target Port

        a. Redirecting to the port the pod(s) are listening to.

    6. Nodes must have public IP addresses.
    7. Use any Node IP + nodePort to access the service.

    #### Load Balancing
    1. Layer 4 Load Balancing

        a. Operating at the Transport Level (TCP). 

        b. Unable to make decisions based on content.

        c. Simple algorithms such as round-robin routing.

    2. Layer 7 Ingress

        a. Operating at the application level (HTTP, SMTP, etc).

        b. Able to make decisions based on the actual content of each message.

        c. More intelligent load balancing decisions and content optimizations. (e.g. routing rules)

    ### Storage & Persistence
    
    #### Volumes
    1. We need to store data outside the container in a Volume.
    2. Volumes let containers store data into external storage systems.
    3. Vendors create plugins for their storage systems according to the container storage interface.

    4. Two ways to create storage

        a. Static and Dynamic (Reclaim Policies are set to `Delete` as the default)

    #### Persistent Volumes and Claims (PV and PVC) -  Storage (the static way)
    1. Persistent Volumes

        a. Represent a storage resource

        b. Cluster wide

        c. Provisioned by an administrator

    2. Persistent volume claim

        a. A one-to-one mapping to a persistent volume.

    3. One or more pods can use a Persistent Volume Claim.

    4. Can be consumed by any of the containers within the pod.

    5. Drawbacks

        a. Waste of resources when a pod only needs a small slice, but no other pods can get a slice during this period.

    6. Use **hostPath** only for local testing. It works only on single-node clusters.

    #### StorageClass - Storage (the dynamic way)
    1. Describes the "classes" of storage offered by the admin.

    2. An abstraction on top of an external storage resource.

    3. No need to set a capacity, as opposed to Persistent Volumes.

    4. Eliminates the need for the admin to pre-provision a persistent volume.

    
    ### ConfigMaps

    1. Allows you to decouple and externalize configuration values.
    
    2. Referenced as environment variables.
    3. Created from:
        
        a. Manifests (yaml files)

        b. Files (text files, etc.)

        c. Directories

    4. Static meaning that if you change values, the containers will have to be restarted to get them.


    ### Secrets
    1. A kind of K8s Objects.

    2. Stored as base64 encoded strings.

    3. Not secure as base54 strings are not encrypted.

    4. Protect them using RBAC authorization policies. 

    5. Store secrets elsewhere if there are sensitive information.

        a. Cloud providers offer ways to secure these secrets.

            1. Azure Key Vault.

            2. AWS Key Management Service.

            3. Google Cloud KMS

    ### Observability

    #### Probes
    1. Start up probes

        a. To know when a container has started.

    2. Readiness probes

        a. To know when a container is ready to accept traffic.

        b. A failing readiness probe will stop the application from receiving traffic.

    3. Liveness probes

        a. Indicates whether the code is running or not.

        b. A failing liveness probe will restart the container.

    4. Probing the container:

        a. The kubelet checks periodically the containers using probes.

        b. ExecAction - execute a command inside the container.

        c. TCPSocketAction - check if a TCP socket port is open.

        d. HTTPGetAction - performs an HTTP GET against a specific port and path.

    ### Dashboards

    1. GUI such as [Lens](https://k8slens.dev/) and [K9s](https://k9scli.io/) (dashboard in a terminal).

    2. Comes with a built-in terminal. 

    ### Scaling Pods
    
    #### Horizontal Pod AutoScaling

    1. Uses the K8s Metrics Server.

    2. Pods must have requests and limits defined.

    3. The HPA checks the Metrics Server every 30 seconds.

    4. Scale according to the min and max number or replicas defined.

    5. Cooldown / Delay

        a. Prevent race conditions.

        b. Once a change has been made, HPA waits.

        c. By default, the delay on scale up events is 3 minutes, and the delay on scale down events is 5 mins.

