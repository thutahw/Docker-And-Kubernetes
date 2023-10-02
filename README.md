# Docker

## Image

Image is the read-only template that tells us how the container will be realized.

## Container

1. Container is the runtime instance of an image that gets created.
2. Containers are ephemerous and stateless. Don't store data here.


## Commands

> docker images

> docker stop xxxx

> docker rm xxxx

> docker build ....

> docker run ....

## Docker Compose

1. Define and run multi-container applications.
2. Define using YAML files.
3. Run using the docker CLI with the compose plugin `Docker compose`.
4. Set Resource Limits (e.g. CPU, Memory) and Environment Variables, Restart Policy.
5. Use for Networking, Dependencies.

    ### Use Cases
    1. Workloads that don't require a full orchestrator.
    2. Development and tests locally.
    3. Use of a service that can run Docker-compose files.

# YAML

1. Human friendly data serialization standard.
2. Used by Docker-Compose and Kubernetes.
3. A superset of JSON.


