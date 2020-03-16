# Nodes
## Master Nodes
- The *Master Node* manages the Kubernetes API server, the Scheduler, and the Controller Manager
    - The **API Server** exposes the Kubernetes API, AKA the frontend of the Kubernetes control plane.
    - The **Scheduler** watches created pods that don't have a Node design yet, and designs the pod to run on a particular
      Node.
    - The **Controller Manager** manages controllers, which run threaded background tasks in a cluster. There are a couple
      separate responsibilities that the Controller Manager has.
      - *Node Controller*: responsible for the state of the worker threads.
      - *Replication Controller*: maintains the correct number of pods for the replicated controllers.
      - *End-Point Controller*: joins services and pods together.
    - **etcd**, a simple distributed key-value store. Kubernetes uses this as its database and stores all of its cluster
      information here - job scheduling info, pod details, stage information, etc.

Interaction with the Master Node is done through `kubectl`, which requires Kubernetes server information and authentication
information to successfully interact with the API Server.

## Worker Nodes
- *Worker Nodes* are where an application is actually run.
    - A node serves as a worker machine in a Kubernetes cluster; it can be either a physical computer or a virtual machine.
    - A node must have a `kubelet` process, container tooling (such as Docker), a `kube-proxy` process, and a process control
      system like `supervisord` to restart components.
    - Worker nodes communicate back to the Master Node via a **Kubelet** process that runs alongside the containerized
    application in each node.
        - Checks with the Master Node to see if pods have been designed to its node, executes containers via the container
        engine, mounts and runs pod volumes and secrets, and reports the current state and health of the pods on its node to
        the Master.
    - The **Kube-proxy** process is the network proxy and load balancer for its worker node.
        - Handles routing of TCP and UDP packets to the pods in its node, performs connection forwarding.
    - The application itself is managed by some containerization application daemon, such as Docker. The constituent
      containers of an application are coupled together in a Pod.

# Pods
- A **Pod** is the smallest unit that can be scheduled as a deployment in a Kubernetes model, and represents a single running process on a cluster. Containers in a Pod share storage, Linux name space, IP addresses, options on
how the containers should be run, etc.
    - *Pods are ephemeral, disposable, do not self-heal, and are not restarted by the scheduler by themselves.*
        Rather than deal with pods directly, higher level constructs such as Controllers should be used.
    - A pod can be in a number of **states**:
        - *Pending:* the pod has been accepted by the Kubernetes system but hasn't been created yet.
        - *Running:* the pod has been scheduled on a node, all of its containers have been created, and at least one is
        in a running state.
        - *Succeeded:* all the containers in the pod have exited with an exit state of 0, and will not be restarted.
        - *Failed:* all the containers in the pod have exited but at least one has returned a non-zero exit code.
        - *CrashLoopBackOff*: when a container fails to start and Kubernetes repeatedly attempts to restart the pod.

# Controllers
Instead of interacting with pods directly, **Controllers** should be used. These provide:
- Application reliability - having multiple instances of an application prevents problems if one or more instance fails.
- Scaling - pods experiencing a high volume of requests can be scaled up automatically.
- Load balancing - running multiple instances of a pod so traffic doesn't overload one single pod or node.

## ReplicaSets
ReplicaSets ensure that a specified number of replicas for a pod are running at all times. If, for example, a pod that a
ReplicaSet controls crashes, another one will be spun up automatically.

***A replicaSet cannot be declared by itself, it must be used as part of a Deployment***.

## Deployments
Deployment controllers provide declarative updates for pods and ReplicaSets.
- The desired state for a deployment can be defined in a YAML file and the Deployment Controller will align the state to
  match.
    - New ReplicaSets can be created or replaced with existing ones as new Deployment configs are deployed.
        - Old ReplicaSets are still kept around in case a rollback is required.

Deployments are useful for:
- *Pod management*: running a ReplicaSet allows deployment of a number of pods, and check their statuses as a single unit.
- *Scaling*: scaling a ReplicaSet also scales out its pods and allows a deployment to handle more traffic.
- *Pod updates and rollbacks* for minor changes to pods, and *pausing and resuming* of the deployment for larger
  changesets.
    - Note that when a deployment is paused, only updates are paused - ***traffic will still be routed to the existing
      ReplicaSet as expected.***
- Observing the *status* of pods in a Deployment to verify health and identify issues during a rollout.

Side note: there used to exist *Replication Controllers*, which was an early implementation of Deployments and ReplicaSets,
but has since been replaced by the latter.

## DaemonSets
DaemonSets are Controllers that ensure all nodes are running a copy of a particular pod.
- As nodes are added or removed from a cluster, a DaemonSet will add or remove its required pods.
- When a DaemonSet is removed, all the pods it created will be removed as well.

Typically a DaemonSet is used for log aggregation or node monitoring.

## Jobs
Jobs are a supervisor process for pods carrying out batch tasks. As these tasks are completed, the Job tracks information
about the completion state of the pod.
- Jobs are for individual processes that run once and must complete successfully.
- Jobs are typically run as cron jobs to execute at specific times.

## Services
A Service provides network connectivity abstraction between pods in different deployments.
- A Service is created with a specific IP address that never changes throughout its lifetime.
- Pods can be configured to use this service to talk to pods in other deployments.
    - This way there's always a fixed IP that a frontend pod can use to communicate with the backend, regardless of any
      deployment changes that may be made to those backend pods.

There are a couple different types of Services:
- *Internal*: IP is only reachable within the cluster - this is a *Cluster IP.*
- *External*: nodes running webservers or or publicly accessible pods can be exposed through an external endpoint. These
  endpoints are available on each node through a specific port, or a *NodePort*.
- *Load Balancer*: exposes application to the internet with load balancing; only available when using Kubernetes in a cloud
  environment backed by a cloud provider.

# Labels, Selectors, and Namespaces
## Labels
Labels are key-value pairs attached to objects like pods, services, and deployments. These are used by Kubernetes users
to identify attributes for objects and don't affect how Kubernetes runs.
- Labels can be added, removed, or changed at any time.
- Label keys are unique per object.

Examples of labels:

|                        |                        |
|------------------------|------------------------|
| `"release" : "stable"` | `"release" : "canary"` |
|                        |                        |

|                          |                        |                                |
|--------------------------|------------------------|--------------------------------|
| `"environment" : "dev" ` | `"environment" : "qa"` | `"environment" : "production"` |
|                          |                        |                                |

|                       |                      |                    |
|-----------------------|----------------------|--------------------|
| `"tier" : "frontend"` | `"tier" : "backend"` | `"tier" : "cache"` |
|                       |                      |                    |

## Selectors
Selectors work in conjunction with Labels to filter out a specific set of objects.

There are two kinds of Selectors:
- *Equality-based*: filters Labels based on equality or inequality of either keys or values.
- *Set-based*: filters based on certain operators.
  - `IN`: matches a value out of a set of defined values.
  - `NOTIN`: matches a value outside of a set of defined values.
  - `EXISTS`: checks to see if a given label exists.

Selectors can be used through `kubectl`.

## Namespaces
Namespaces allow for multiple virtual clusters to be backed by the same physical cluster.
- Namespaces are useful in large enterprises where there are multiple users and teams that require access to resources, but
  only to resources that they have ownership of.
- Namespaces can also be used to divide cluster resources between users, via *resource quotas.*
- Namespaces provide scoping for names - names can be identical so long as they're in different namespaces.

# Miscellaneous

`minikube` can be used to spin up a cluster locally.
    - Spins up a VM and deploys a simple, single-node cluster on a local machine.