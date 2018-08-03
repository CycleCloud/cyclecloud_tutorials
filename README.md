# Azure CycleCloud Tutorials

These are technical labs to help you get started using CycleCloud to create, use, and manage Azure HPC clusters. 

## Labs

- [ ] 1. [Lab 1 - Setup Azure CycleCloud via ARM + Creating an autoscaling HPC Cluster](/Lab1/Tutorial.md)
- [ ] 2. [Lab 2 - Customizing an HPC cluster template](/Lab2/Tutorial.md)
- [ ] 3. [Lab 3 - Deploy a new application to an HPC cluster](/Lab3/Tutorial.md)
- [ ] 4. [Lab 4 - Customizing an HPC Cluster Template](/Lab4/Tutorial.md)
- [ ] 5. [Lab 5 - Connecting a standalone NFS file-system](/Lab5/Tutorial.md)

## Objectives
In these labs, you will:

* Setup an Azure CycleCloud VM using an ARM template, and configure it with Azure credentials
* Create a simple HPC cluster consisting of a job scheduler and an NFS file server, and running the [LAMMPS](https://lammps.sandia.gov/) molecular dynamics simulator application
* Submit jobs and observe the cluster autoscale up and down automatically
* Customize the cluster template to add persistent storage to the NFS file server
* Add a new application to CycleCloud, and deploy it on an HPC cluster
* Mount an external NFS file system to an HPC cluster

Additional resources can be located at the end of the Lab, as well as links for more advanced topics. These labs should take no more than 30-60 minutes to complete per lab, and many much faster than that.

We welcome any thoughts or feedback. We are always looking for ways to improve the experience of learning Azure CycleCloud!

## Azure CycleCloud

Azure CycleCloud provides a simple, secure, and scalable way to manage compute and storage resources for HPC workloads in Microsoft Azure. Azure CycleCloud enables users to create environments for workloads on any point of the parallel and distributed processing spectrum, from parallel workloads to tightly-coupled applications such as MPI jobs on Infiniband/RDMA. By managing resource provisioning, configuration, and monitoring, Azure CycleCloud allows users and IT staff to focus on business needs instead infrastructure.

Azure CycleCloud delivers:

* Complete control over compute environments, including VM resources, storage, networking, and the fuCusll application stack
* Data transfer and management tools
* Role-based access control (RBAC)
* Templated applications and reference architectures
* Cost reporting and controls
* Monitoring and alerting
* Automated, customizable configuration
* Consistent security and encryption

If this is your first time using Azure CycleCloud, we recommend reading the [product documentation](https://docs.microsoft.com/en-us/azure/cyclecloud) to get more familiar with common Azure CycleCloud concepts: clusters, nodes and node arrays, data management, etc. Azure CycleCloud is freely available, downloadable, packaged, licensed application. For support options or other general questions, email askcyclecloud @ microsoft.com.

## Prerequisites

- Azure subscription (You can sign up for a free account here.)

## Intended audience

This lab is intended for people who would like to learn how to use Azure CycleCloud to create, customize, and manage HPC environments in Azure.

## Contributing

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

![CycleCloud Logo](Cloud_Cycle_256.png)

