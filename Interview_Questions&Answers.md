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

-**What is deployment and service? How does the traffic from outside world to container?**

   - Deployment is the desired state of the application.

   - In Kubernetes, a deployment is typically defined using a YAML file. The YAML file contains the configuration for the deployment, specifying details such as the number of replicas, the container image, and other settings. Below is a basic example of a Kubernetes deployment YAML file:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: example-container
        image: your-container-image:tag
        ports:
        - containerPort: 80
```

Let's break down the key components of this YAML file:

- `apiVersion`: Specifies the API version being used. In this case, it's `apps/v1` for the Deployment resource.

- `kind`: Specifies the type of resource, which is a `Deployment` in this example.

- `metadata`: Contains metadata about the deployment, including its name.

- `spec`: Describes the desired state of the deployment.

  - `replicas`: Specifies the desired number of replicas (instances) of the application to run, in this case, 3.

  - `selector`: Defines how the Deployment identifies which Pods to manage. In this case, it uses labels to select Pods with the label `app: example`.

  - `template`: Describes the Pod template that will be used to create new Pods.

    - `metadata`: Contains labels that will be applied to the Pods.

    - `spec`: Describes the specification of the Pods.

      - `containers`: Specifies the containers to run in the Pod.

        - `name`: The name of the container.

        - `image`: The container image and its version/tag.

        - `ports`: Specifies the ports that should be exposed by the container.

You can customize this template according to your application's requirements. For example, you would replace `your-container-image:tag` with the actual name and version of your container image.

To deploy this YAML file to a Kubernetes cluster, you would typically use the `kubectl apply` command. Assuming the YAML is in a file called `deployment.yaml`, you would run:

```bash
kubectl apply -f deployment.yaml
```

This will create the deployment and the associated Pods in your Kubernetes cluster.

-**What is a service? How does the traffic from outside world access container?**

   In Kubernetes, a Service is an abstraction that defines a set of Pods and a policy to access them. It provides a stable endpoint (IP address and a port) to access the application running in the Pods. Services allow decoupling of the internal implementation details of your application from the way client applications can consume it. Here is an example of a basic Service definition in a Kubernetes YAML file:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: example-service
spec:
  selector:
    app: example
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: NodePort
```

Let's break down the key components of this YAML file:

- `apiVersion`: Specifies the API version, and in this case, it's `v1`.

- `kind`: Specifies the type of resource, and it's a `Service`.

- `metadata`: Contains metadata about the service, including its name.

- `spec`: Describes the desired state of the service.

  - `selector`: Specifies a set of labels that the service will use to select Pods. In this example, the service will route traffic to Pods with the label `app: example`.

  - `ports`: Specifies the ports on which the service will listen.

    - `protocol`: Specifies the protocol used for the service, which is `TCP` in this case.

    - `port`: Specifies the port on which the service will listen within the cluster.

    - `targetPort`: Specifies the port on which the Pods selected by the service will be contacted.

  - `type`: Specifies the type of service.  type field, which is set to NodePort. This means that the service will be accessible on a port on every node in the cluster.

When you create a NodePort Service, Kubernetes will allocate a high-numbered port (in the range 30000-32767) on each node. You can also specify a specific port by setting the nodePort field in the ports section.

When you create a NodePort Service, Kubernetes will allocate a high-numbered port (in the range 30000-32767) on each node. You can also specify a specific port by setting the nodePort field in the ports section, like this:

      ports:
        - protocol: TCP
          port: 80
          targetPort: 8080
          nodePort: 30080
In this example, the service will be accessible on port 30080 on every node in the cluster.

To create or update the service in your cluster, you can use the kubectl apply command:

      kubectl apply -f service-nodeport.yaml
      
After the service is created, you can access it from outside the cluster using any node's IP address and the assigned NodePort. For example, if your node's IP address is NODE_IP, and the NodePort is 30080, you can access the service at http://NODE_IP:30080.

It's important to note that while NodePort Services provide external access, they might not be suitable for production environments where you typically use a LoadBalancer Service or an Ingress resource to handle external traffic more effectively.

-
When traffic from the outside world needs to reach a container within a Kubernetes cluster, there are different approaches, and the choice depends on the requirements of your application and infrastructure. Here are some common ways to route external traffic to containers in a Kubernetes cluster:

NodePort Service:

A NodePort service exposes a service externally by opening a static port on each node in the cluster.
Traffic sent to the node's IP address on the allocated NodePort is forwarded to the service.
Suitable for development and testing but may not be ideal for production due to security concerns and limitations in handling load balancing.
LoadBalancer Service:

A LoadBalancer service provisioned by a cloud provider assigns an external IP address and distributes traffic across the cluster nodes.
Suitable for production as it provides external load balancing. The cloud provider's load balancer manages traffic distribution to the nodes.
Ingress Controller:

Ingress is an API object in Kubernetes that manages external access to services within a cluster.
Ingress controllers are components that handle the ingress traffic and apply rules to determine how the traffic should be routed.
Ingress allows for more sophisticated routing based on hostnames, paths, etc.

-**What are different types of services?**

In Kubernetes, services are used to expose applications running in Pods to network traffic. There are several types of services, each serving different use cases and providing different levels of access and exposure. Here are the main types of services in Kubernetes:

1. **ClusterIP:**
   - `ClusterIP` is the default service type.
   - Exposes the service on a cluster-internal IP address.
   - The service is only accessible within the cluster.

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: my-service
   spec:
     selector:
       app: my-app
     ports:
       - protocol: TCP
         port: 80
         targetPort: 8080
   ```

2. **NodePort:**
   - Exposes the service on each Node's IP at a static port (NodePort).
   - Allows external traffic to reach the service.
   - Useful for development and testing but may not be suitable for production.

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: my-service
   spec:
     selector:
       app: my-app
     ports:
       - protocol: TCP
         port: 80
         targetPort: 8080
     type: NodePort
   ```

3. **LoadBalancer:**
   - Exposes the service externally using a cloud provider's load balancer.
   - Automatically assigns an external IP and manages traffic distribution.
   - Suitable for production environments.

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: my-service
   spec:
     selector:
       app: my-app
     ports:
       - protocol: TCP
         port: 80
         targetPort: 8080
     type: LoadBalancer
   ```

4. **ExternalName:**
   - Maps the service to the contents of the `externalName` field (e.g., a DNS name).
   - Allows services within the cluster to access external services by name.
   - Not used for load balancing.

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: my-service
   spec:
     type: ExternalName
     externalName: example.com
   ```

5. **Headless Service:**
   - A headless service does not allocate an IP address.
   - Useful for scenarios where you need to discover individual Pod IP addresses rather than accessing them through a single IP.

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: my-service
   spec:
     clusterIP: None
     selector:
       app: my-app
     ports:
       - protocol: TCP
         port: 80
         targetPort: 8080
   ```

These service types allow you to control how applications running in Pods are exposed and accessed within and outside the cluster. The choice of service type depends on factors such as the desired level of accessibility, the environment (development, testing, production), and the infrastructure in use (on-premises, cloud provider).

-**Which service we can use securily if we want to open traffic to external world?**

   If you want to securely expose your application to the external world in a Kubernetes cluster, the recommended approach is to use the `LoadBalancer` service type or an `Ingress` resource with TLS termination. Here's a brief overview of each:

1. **LoadBalancer Service:**
   - The `LoadBalancer` service type is suitable for securely exposing services to the external world. It leverages the cloud provider's load balancer, which can handle external traffic and distribute it to the nodes in your cluster.
   - The service automatically assigns an external IP address and manages the load balancing.
   - It's important to note that not all Kubernetes clusters support the `LoadBalancer` service type. This type relies on the underlying cloud provider to provision external load balancers.

   Example YAML for a LoadBalancer service:
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: my-service
   spec:
     selector:
       app: my-app
     ports:
       - protocol: TCP
         port: 80
         targetPort: 8080
     type: LoadBalancer
   ```

2. **Ingress with TLS Termination:**
   - Ingress is an API object in Kubernetes that provides HTTP and HTTPS routing to services based on rules.
   - If your application requires HTTPS, you can use an `Ingress` resource along with TLS termination.
   - TLS termination allows the Ingress controller to handle SSL/TLS encryption and forward decrypted traffic to the appropriate services.

   Example YAML for an Ingress resource with TLS termination:
   ```yaml
   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: my-ingress
   spec:
     tls:
     - hosts:
       - mydomain.com
       secretName: my-tls-secret
     rules:
     - host: mydomain.com
       http:
         paths:
         - path: /
           pathType: Prefix
           backend:
             service:
               name: my-service
               port:
                 number: 80
   ```

   In this example, replace `mydomain.com` with your domain, and `my-tls-secret` with the name of your TLS secret containing the SSL certificate and private key.

When exposing services to the external world, ensure that you configure appropriate security measures, such as using HTTPS for encrypted communication, securing access with proper authentication and authorization mechanisms, and regularly updating security policies and certificates.

-**What is difference b/w label and annotation?**

Certainly! Let's simplify the difference between labels and annotations in Kubernetes:

**Labels:**

- **Purpose:** Labels are like sticky notes that help you categorize and organize objects.
  
- **Use Case:**
  - Use them to group and select objects.
  - For example, you might label Pods with `app=myapp` to group them together.

- **Example:**
  ```yaml
  metadata:
    labels:
      app: myapp
      tier: frontend
  ```

**Annotations:**

- **Purpose:** Annotations are like comments or extra information for yourself and others.
  
- **Use Case:**
  - Use them to store extra details that are not used for grouping.
  - For example, you might annotate an object with `buildVersion: 1.2.3` or `createdBy: JohnDoe`.

- **Example:**
  ```yaml
  metadata:
    annotations:
      buildVersion: 1.2.3
      createdBy: JohnDoe
  ```

**In Simpler Terms:**

- **Labels:** Think of them as colored tags on objects to organize and find them easily.

- **Annotations:** Think of them as comments or notes attached to objects, providing additional information that might be useful to you or other tools.

So, labels help you organize and find things, while annotations are notes that provide extra information about those things.

-**
