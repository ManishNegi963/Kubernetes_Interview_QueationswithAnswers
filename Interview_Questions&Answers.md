## INTERVIEW QUESTIONS WITH ANSWERS

-**What is the Architecture of K8s and components usage?**

   - Refer to this blog: https://journeytodevops.hashnode.dev/container-orchestration-kubernetes-architecture

-**What is a pod?**

   - A Pod is a smallest deplyable unit in K8s which consist of application running on docker container inside the pod, which can be auto healed, auto scaled and auto replicate.
   - A pod can be create using manifest file.

   - In Kubernetes (K8s), a "Pod" is the smallest deployable unit and the basic building block of the deployment in the Kubernetes architecture. A Pod represents a group of one or more containers that are scheduled together on the same host and share the same network namespace, storage, and set of specifications. Containers within a Pod share the same IP address and port space, and they can communicate with each other using localhost.

Here are some key points about Pods:

1. **Container(s):** A Pod encapsulates one or more containers. These containers are tightly coupled and share the same resources, such as storage volumes and network namespace.

2. **Atomic Unit:** The Pod is considered the smallest deployable unit in Kubernetes. It represents a single instance of a running process in a cluster.

3. **Shared Context:** Containers within a Pod share the same network namespace, meaning they can communicate with each other using localhost. They also share the same storage volumes, making it easy for them to share data.

4. **Pod Lifecycle:** The lifecycle of a Pod is managed by the Kubernetes control plane. When a Pod is scheduled to a node, the containers within the Pod are started. If a Pod is deleted, all of its containers are terminated.

5. **Atomic Updates:** Containers within the same Pod are typically tightly coupled, and they are often deployed and scaled together. This makes it easy to perform atomic updates to a group of containers.

Here's a simple example of a Pod definition in Kubernetes YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: container1
    image: nginx:latest
  - name: container2
    image: busybox:latest
```

In this example, the Pod named `mypod` has two containers (`container1` and `container2`). Both containers share the same network namespace and storage volumes. They run different images, demonstrating the flexibility of running multiple containers within the same Pod.

While Pods can have multiple containers, it's important to note that a Pod is intended to represent a single instance of a coherent application, and the containers within a Pod are expected to work together closely. For more complex scenarios, higher-level abstractions like Deployments and StatefulSets are often used to manage the deployment and scaling of Pods.

-**
