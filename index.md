## Tanzu Kubernetes Grid Service 
### Quick & Dirty - Getting Started Guide

The Tanzu Kubernetes Grid Service provides self-service lifecycle management of Tanzu Kubernetes clusters. You use the Tanzu Kubernetes Grid Service to create and manage Tanzu Kubernetes clusters in a declarative manner that is familiar to Kubernetes operators and developers.

vSphere with Tanzu transforms vSphere to a platform for running Kubernetes workloads natively on the hypervisor layer. When enabled on a vSphere cluster, vSphere with Tanzu provides the capability to run Kubernetes workloads directly on ESXi hosts and to create upstream Kubernetes clusters within dedicated resource pools. [Read more](https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-4D0D375F-C001-4F1D-AAB1-1789C5577A94.html)


### Tanzu Kubernetes Grid Service General Architecture 


![TKGS](https://raw.githubusercontent.com/jrobinsonvm/tkgs-quickstart/gh-pages/TKGS.png)



### Supervisor Cluster Architecture
![supervisorcluster](https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/images/GUID-FB8FF18C-165F-4DE7-A36B-39BD2A50EBEC-high.png)

A cluster that is enabled for vSphere with Tanzu is called a Supervisor Cluster. It runs on top of an SDDC layer that consists of ESXi for compute, NSX-T Data Center or vSphere networking, and vSAN or another shared storage solution. Shared storage is used for persistent volumes for vSphere Pods, VMs running inside the Supervisor Cluster, and pods in a Tanzu Kubernetes cluster. After a Supervisor Cluster is created, as a vSphere administrator you can create namespaces within the Supervisor Cluster that are called vSphere Namespaces. As a DevOps engineer, you can run workloads consisting of containers running inside vSphere Pods and create Tanzu Kubernetes clusters.


### vSphere Namespace 
![namespace](https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/images/GUID-8ED85D0A-FFEF-438A-930C-B824AEDE0728-high.png)

A vSphere Namespace sets the resource boundaries where vSphere Pods and Tanzu Kubernetes clusters created by using the Tanzu Kubernetes Grid Service can run. When initially created, the namespace has unlimited resources within the Supervisor Cluster. As a vSphere administrator, you can set limits for CPU, memory, storage, as well as the number of Kubernetes objects that can run within the namespace. A resource pool is created per each namespace in vSphere. Storage limitations are represented as storage quotas in Kubernetes.

### vSphere Content Library
A vSphere Content Library provides the virtual machine template used to create the Tanzu Kubernetes cluster nodes. For each Supervisor Cluster where you intend to deploy a Tanzu Kubernetes cluster, you must define a Subscribed Content Library object that sources the OVA used by the Tanzu Kubernetes Grid Service to build cluster nodes. The same Subscribed Content Library can be configured for multiple Supervisor Clusters. There is no relationship between the Subscribed Content Library and the vSphere Namespace. The Subscribed Content Library downloads the latest templates directly from VMware. You upload the OVA templates you want to use to a Local Content Library.


### Tanzu Kubernetes Grid Cluster Architecture 
![tkc](https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/images/GUID-389BC19D-1AC5-4F1E-9055-2B8903AFC342-high.png)

A Tanzu Kubernetes cluster is a full distribution of the open-source Kubernetes software that is packaged, signed, and supported by VMware. In the context of vSphere with Tanzu, you can use the Tanzu Kubernetes Grid Service to provision Tanzu Kubernetes clusters on the Supervisor Cluster. You can invoke the Tanzu Kubernetes Grid Service API declaratively by using kubectl and a YAML definition.

A Tanzu Kubernetes cluster resides in a vSphere Namespace. You can deploy workloads and services to Tanzu Kubernetes clusters the same way and by using the same tools as you would with standard Kubernetes clusters.



### What are my deployment options?  




### What are my vSphere with Tanzu Networking Options?

A Supervisor Cluster can either use the vSphere networking stack or VMware NSX-T™ Data Center to provide connectivity to Kubernetes control plane VMs, services, and workloads. The networking used for Tanzu Kubernetes clusters provisioned by the Tanzu Kubernetes Grid Service is a combination of the fabric that underlies the vSphere with Tanzu infrastructure and open-source software that provides networking for cluster pods, services, and ingress.



### Supervisor Cluster Networking with NSX-T Data Center ( Our Focus) 

VMware NSX-T Data Center™ provides network connectivity to the objects inside the Supervisor Cluster and external networks. Connectivity to the ESXi hosts comprising the cluster is handled by the standard vSphere networks.

You can also configure the Supervisor Cluster networking manually by using an existing NSX-T Data Center deployment or by deploying a new instance of NSX-T Data Center.


![nsx-t](https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/images/GUID-3ACAC5F4-AC41-4602-A648-E4D02449B7BA-high.png)




Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/jrobinsonvm/tkgs-quickstart/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.


Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).
