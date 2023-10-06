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
    2. Pod runs in a Node.
    3. Node runs in a Cluster.

    ### CLI
    1. `kubectl` communicates with the apiserver.
    2. Configuration stored locally. <br>
        a. ${HOME}/,kube/config <br>
        b. C:\Users\{USER}\.kube\config.
    
    ### Context
    1. A context is a group of access parameters to a K8s cluster.
    2. Contains a kubernetes cluster, a user, and a namespace.
    3. The current context is the cluster that is currently the default for kubectl. <br>
        a. All kubectl commands run against that cluster. <br>
    4. 