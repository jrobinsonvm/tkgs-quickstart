## Tanzu Kubernetes Grid Service 
### Quick & Dirty - Getting Started Guide

The Tanzu Kubernetes Grid Service provides self-service lifecycle management of Tanzu Kubernetes clusters. You use the Tanzu Kubernetes Grid Service to create and manage Tanzu Kubernetes clusters in a declarative manner that is familiar to Kubernetes operators and developers.

vSphere with Tanzu transforms vSphere to a platform for running Kubernetes workloads natively on the hypervisor layer. When enabled on a vSphere cluster, vSphere with Tanzu provides the capability to run Kubernetes workloads directly on ESXi hosts and to create upstream Kubernetes clusters within dedicated resource pools. [Read more](https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-4D0D375F-C001-4F1D-AAB1-1789C5577A94.html)


### Tanzu Kubernetes Grid Service General Architecture 


![TKGS](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/TKGS.png)



### Supervisor Cluster Architecture
![supervisorcluster](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/%E2%80%A2Center%20Server.png)

A cluster that is enabled for vSphere with Tanzu is called a Supervisor Cluster. It runs on top of an SDDC layer that consists of ESXi for compute, NSX-T Data Center or vSphere networking, and vSAN or another shared storage solution. Shared storage is used for persistent volumes for vSphere Pods, VMs running inside the Supervisor Cluster, and pods in a Tanzu Kubernetes cluster. After a Supervisor Cluster is created, as a vSphere administrator you can create namespaces within the Supervisor Cluster that are called vSphere Namespaces. As a DevOps engineer, you can run workloads consisting of containers running inside vSphere Pods and create Tanzu Kubernetes clusters.


### vSphere Namespace 
![namespace](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Supervisor%20Cluster.png)

A vSphere Namespace sets the resource boundaries where vSphere Pods and Tanzu Kubernetes clusters created by using the Tanzu Kubernetes Grid Service can run. When initially created, the namespace has unlimited resources within the Supervisor Cluster. As a vSphere administrator, you can set limits for CPU, memory, storage, as well as the number of Kubernetes objects that can run within the namespace. A resource pool is created per each namespace in vSphere. Storage limitations are represented as storage quotas in Kubernetes.

### vSphere Content Library
A vSphere Content Library provides the virtual machine template used to create the Tanzu Kubernetes cluster nodes. For each Supervisor Cluster where you intend to deploy a Tanzu Kubernetes cluster, you must define a Subscribed Content Library object that sources the OVA used by the Tanzu Kubernetes Grid Service to build cluster nodes. The same Subscribed Content Library can be configured for multiple Supervisor Clusters. There is no relationship between the Subscribed Content Library and the vSphere Namespace. The Subscribed Content Library downloads the latest templates directly from VMware. You upload the OVA templates you want to use to a Local Content Library.


### Tanzu Kubernetes Grid Cluster Architecture 
![tkc](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/tkgc.png)

A Tanzu Kubernetes cluster is a full distribution of the open-source Kubernetes software that is packaged, signed, and supported by VMware. In the context of vSphere with Tanzu, you can use the Tanzu Kubernetes Grid Service to provision Tanzu Kubernetes clusters on the Supervisor Cluster. You can invoke the Tanzu Kubernetes Grid Service API declaratively by using kubectl and a YAML definition.

A Tanzu Kubernetes cluster resides in a vSphere Namespace. You can deploy workloads and services to Tanzu Kubernetes clusters the same way and by using the same tools as you would with standard Kubernetes clusters.



### What are my vSphere with Tanzu Networking Options?

A Supervisor Cluster can either use the vSphere networking stack or VMware NSX-T™ Data Center to provide connectivity to Kubernetes control plane VMs, services, and workloads. The networking used for Tanzu Kubernetes clusters provisioned by the Tanzu Kubernetes Grid Service is a combination of the fabric that underlies the vSphere with Tanzu infrastructure and open-source software that provides networking for cluster pods, services, and ingress.



### Supervisor Cluster Networking with NSX-T Data Center ( Our Focus) 

VMware NSX-T Data Center™ provides network connectivity to the objects inside the Supervisor Cluster and external networks. Connectivity to the ESXi hosts comprising the cluster is handled by the standard vSphere networks.

You can also configure the Supervisor Cluster networking manually by using an existing NSX-T Data Center deployment or by deploying a new instance of NSX-T Data Center.


![nsx-t](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/nsx-t.png)


- NSX Container Plug-in (NCP) provides integration between NSX-T Data Center and Kubernetes. The main component of NCP runs in a container and communicates with NSX Manager and with the Kubernetes control plane. NCP monitors changes to containers and other resources and manages networking resources such as logical ports, segments, routers, and security groups for the containers by calling the NSX API.   The NCP creates one shared tier-1 gateway for system namespaces and a tier-1 gateway and load balancer for each namespace, by default. The tier-1 gateway is connected to the tier-0 gateway and a default segment.   System namespaces are namespaces that are used by the core components that are integral to functioning of the supervisor cluster and Tanzu Kubernetes. The shared network resources that include the tier-1 gateway, load balancer, and SNAT IP are grouped in a system namespace.

- NSX Edge provides connectivity from external networks to Supervisor Cluster objects. The NSX Edge cluster has a load balancer that provides a redundancy to the Kubernetes API servers residing on the control plane VMs and any application that must be published and be accessible from outside the Supervisor Cluster.

- A tier-0 gateway is associated with the NSX Edge cluster to provide routing to the external network. The uplink interface uses either the dynamic routing protocol, BGP, or static routing.

- Each vSphere Namespace has a separate network and set of networking resources shared by applications inside the namespace such as, tier-1 gateway, load balancer service, and SNAT IP address.

- Workloads running in Sphere Pods, regular VMs, or Tanzu Kubernetes clusters, that are in the same namespace, share a same SNAT IP for North-South connectivity.
Workloads running in Sphere Pods or Tanzu Kubernetes clusters will have the same isolation rule that is implemented by the default firewall.

- A separate SNAT IP is not required for each Kubernetes namespace. East west connectivity between namespaces will be no SNAT.

- The segments for each namespace reside on the vSphere Distributed Switch (VDS) functioning in Standard mode that is associated with the NSX Edge cluster. The segment provides an overlay network to the Supervisor Cluster.

- Supervisor clusters have separate segments within the shared tier-1 gateway. For each Tanzu Kubernetes cluster, segments are defined within the tier-1 gateway of the namespace.

- The Spherelet processes on each ESXi hosts communicate with vCenter Server through an interface on the Management Network.


### Pre-requistes for configuring a supervisor cluster with NSX-T as the networking stack 

![nsx-t-reqs](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/reqnsx.png)



### What are my deployment options for vSphere with Tanzu and NSX-T?


#### Topology for a Management, Edge, and Workload Domain Cluster

You can deploy vSphere with Tanzu with combined management, Edge, and workload management functions on a single vSphere cluster.

![allinonedeployment](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/ext.png)


#### Topology with Separate Management and Edge Cluster and Workload Management Cluster

You can deploy vSphere with Tanzu in two clusters, one cluster for the Management and Edge functions, and another one dedicated to Workload Management.

![separatedeployment](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/ext2.png)


------

### Now its time to Enable Workload Management

#### Step 0 - Navigate to Workload Management and select "Get Started"

![wloadmgmt](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Workload%20Management.png)


#### Step 1 - Select your preferred networking stack.  If using NSX-T select NSX, otherwise select vSphere Distributed Switch (VDS) 

![wloadmgmt1](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Pasted%20Graphic%201.png)

#### Step 2 - Select your vSphere cluster you would like to make your supervisor cluster.   

![wloadmgmt2](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Pasted%20Graphic%202.png)

#### Step 3 - Select your storage policy that was created earlier and assign it.   

![storage](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Pasted%20Graphic%203.png)

#### Step 4 - Configure the Management Network 

![mgmtnet](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Pasted%20Graphic%204.png)

#### Step 5 - Configure the Workload Network 

![workload](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Pasted%20Graphic%205.png)

#### Step 6 - Set your content library 

![content](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Pasted%20Graphic%206.png)

#### Step 7 - Configure the size of your control plane nodes and optionally configure a DNS Name for the API Server. Once complete, select Finish.   

![api](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Pasted%20Graphic%207.png)

#### Step 8 - Wait for Workload Management to complete.  
![done](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Pasted%20Graphic%208.png)

#### Step 9 - Once Workload Management completes, select the Namespace tab to create a new Namespace to deploy your first Kubernetes Cluster.   
![done](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Pasted%20Graphic%209.png)

#### Step 10 - Navigate to your newly created namespace.     
![done](https://github.com/jrobinsonvm/tkgs-quickstart/blob/gh-pages/images/Pasted%20Graphic%2010.png?raw=true)

#### Step 11 - Add Permissions (RBAC) to your namespace by selecting the Add Permissions button under Permissions.  Users or Groups can be added.   
![done](https://github.com/jrobinsonvm/tkgs-quickstart/blob/gh-pages/images/Pasted%20Graphic%2011.png?raw=true)

#### Step 12 - Add a Storage Policy to your namespace by selecting the Add Storage button under Storage.  Select the storage policy we created earlier.   Kubernetes Persistent Volumes will utilize the underlying storage.   
![done](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Pasted%20Graphic%2012.png)

#### Step 13 - Set resource limits for your namespace by selecting the Edit Limits button under Capacity and Usage.  This allows you to provide self service access with guardrails.     
![done](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Pasted%20Graphic%2013.png)

#### Step 14 - Add VM Classes you wish to associate with your namespace by selecting Manage VM Classes under VM Service.        
![done](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/images/Pasted%20Graphic%2014.png)




-----

For more details see the offical [vSphere with Tanzu Docs](https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-152BE7D2-E227-4DAA-B527-557B564D9718.html).
