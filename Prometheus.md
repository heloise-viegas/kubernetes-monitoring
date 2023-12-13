# Monitoring on Kubernetes - Part 1

![image](https://github.com/heloise-viegas/kubernetes-monitoring/assets/37453877/6d89d7fc-cdba-4ec4-be87-6229ffe3c745)

Prometheus is a well-known Kubernetes monitoring tool. There are easier ways to install and configure Prometheus be it Helm charts or managed services on managed clusters.
But as a Kubernetes admin, one needs to know how this tool works at its very core.
Hence this is a documentation of my learnings and perspective with this tool.

This was my second attempt at learning Prometheus and has been far more successful than the first thanks to this [Tutorial](https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/) by Bibin Wilson at [Devopscube.](https://devopscube.com/)
You could also use this tutorial as an example for RBAC, Config Maps and Deployments.

**P.S.** Please have a look at the tutorial link for complete steps. This document is just add-on notes from my perspective.

# Prometheus
Prometheus scrapes metrics from all Kubernetes resources. This is helpful to setup resource limits in pods and also to create various alerts.
### How does it work?

As many of you may know this [Video](https://youtu.be/h4Sl21AKiDg?si=aW4Sh00qy7r-8cdC) by [Tech World With Nana](https://www.techworld-with-nana.com/) beautifully explains how Prometheus works. It is a must watch to understand and setup Prometheus. 

## Resources 
The Kubernetes resources that were created to setup Prometheus are as follows:
- Namespace: monitoring
- Cluster Role: prometheus
- Cluster Role Binding: prometheus
- Service Account: default (created by k8s in monitoring namespace)
- Config Map: prometheus-server-conf
- Deployment: prometheus-deployment


![image](https://github.com/heloise-viegas/kubernetes-monitoring/assets/37453877/e5e03b81-6cc2-4414-aeb3-6d7be75d4723)
 
### RBAC
- Role-based access control (RBAC) is a method of managing access to resources based on the kind of roles of individual users.
- To implement RBAC we need an Identity(User/Group/ServiceAccount) that is bound to a Role via a RoleBinding.
- In Kubernetes, we have 2 types of RBAC: 
    - Service Account, Role, RoleBinding (for access within a namespace)
    - Service Account, ClusterRole, ClusterRoleBinding (for access across namespaces)
- For Prometheus, we need metrics across namespaces hence we use ClusterRole and   ClusterRoleBinding.

### The Cluster Role: prometheus
 - ClusterRole contains rules or set of permissions.
 - Each rule is made up 3 elements:
	 - apiGroups (the api group of the resource)
	 - resources (e.g. pods, nodes, services)
	 - verbs (action that can be performed on the resource e.g. get, list, watch)
 - Prometheus ClusterRole has 3 rules:
	 - Rule1 is for nodes, services, pods etc.
	 - Rule 2 is for ingress
	 - Rule 3 is for the metrics endpoint

### The Cluster Role Binding: prometheus
- ClusterRoleBinding binds ServiceAccount to the ClusterRole
- I.e. The ServiceAccount is granted all permissions specified in the ClusterRole via the rules.
- ClusterRoleBinding contains 2 elements:
	 - roleRef (name of the ClusterRole)
	 - subjects (ServiceAccounts/User/Group)
- Prometheus ClusterRoleBinding has the below specifications:
	- roleRef is ClusterRole prometheus
	- subject is ServiceAccount default from monitoring namespace

### The Service Account: default
- It is a non-human account that is used as an identity for Kubernetes resources to access external applications.
- The default ServiceAccount is created by Kubernetes in every namespace when a cluster is created.
- Prometheus uses Kubernetes Api to get metrics of the resources, for this it requires read permissions.
- To achieve this, we use the default Service Account from the monitoring namespace.

### The Config Map: prometheus-server-conf
   - Stores non-confidential configurational data
   - Can be accessed in Pods as environment variables, command-line arguments, or as configuration files in a volume.
   - We have used the later i.e. configuration files in a volume
   -  The prometheus-server-conf creates 2 files namely **prometheus.yaml** and **prometheus.rules** which are mounted as a volume inside the Prometheus container at /etc/prometheus path.
   - prometheus.yaml has scrape config which is needed for Prometheus
	- prometheus.rules has alerting rules which will be used later in the alerting section.

### The Deployment: prometheus-deployment
- The deployment specifies the replicas, container specifications and volumes.
- The config-map we created above is passed as a command-line argument in the **args** field as **"--config.file=/etc/prometheus/prometheus.yml"**
- /etc/prometheus is the path where the file is stored in the volume, as mentioned in the above section.
- **"--storage.tsdb.path=/prometheus/"** specifies the storage path mentioned in the mountPath under prometheus-storage-volume
- There are 2 volumes that are created
	- volume 1 is created from the config map **prometheus-server-conf** and is used   to access the scrape configs
	- volume 2 is a shared volume of type EmptyDir , used for storage that is not persistent.

![image](https://github.com/heloise-viegas/kubernetes-monitoring/assets/37453877/edc02ab5-f9a8-40ee-b52d-4f8f01cf5169)

This brings us to the end of part1, where I have explicitly noted down points that could be difficult to understand in the first attempt.
As you may have noticed I have overlooked many basic concepts like container image, labels, port-forwarding, since these are well-known and easily understood by most of us who deal daily with Kubernetes.
