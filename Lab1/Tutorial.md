# Setup Azure CycleCloud via ARM + Creating an autoscaling HPC Cluster

This lab focuses on helping you become familiar with Azure CycleCloud, a tool
for orchestrating HPC clusters in Azure.

Please send questions or comments to the Azure CycleCloud PM team -
<mailto:askcyclecloud @ microsoft.com>

## Goals
* Create a fully functional, configured Azure CycleCloud instance that can be
  used for further labs, pilot deployments, etc. 
* Learn how to configure and deploy a LAMMPS HPC cluster, one of the standard
  cluster types included with Azure CycleCloud.

## Pre-requisites
* Standard lab
  [prerequisites](https://github.com/CycleCloud/cyclecloud_tutorials/blob/master/README.md#prerequisites)
  

## 1. Starting An Azure CycleCloud Server

There are several ways to install and setup an Azure CycleCloud server. For this
lab, you'll be deploying Azure CycleCloud onto a VM via an ARM template. 

As you follow the steps below, *please keep track of the following*: 1. The
    domain name (FQDN) of your Azure CycleCloud. 2. The `username` used in the
    ARM template. If you followed the QSG, the same `username` should be used in
    the Azure CycleCloud web UI. 3. The `password` created in the Azure
    CycleCloud web UI for your user.


### 1.1 Log into https://shell.azure.com
```
Requesting a Cloud Shell.Succeeded.
Connecting terminal...

Welcome to Azure Cloud Shell

Type "az" to use Azure CLI 2.0
Type "help" to learn about Cloud Shell
ellen@Azure:~$
```

### 1.2 Figure out your username
```
ellen@Azure:~$ whoami
ellen
ellen@Azure:~$
```

### 1.3 Generate an SSH keypair (if you do not already have one)
* In Cloud Shell, run:
```
ellen@Azure:~$ ssh-keygen -f ~/.ssh/id_rsa  -N "" -b 4096
Generating public/private rsa key pair.
Your identification has been saved in /home/ellen/.ssh/id_rsa.
Your public key has been saved in /home/ellen/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:L8DFLyfXbKQT5PZZFwTGFnAY4ODCtYFyhGhoHFTpbKM ellen@cc-c6733553-7b96c85fb8-hbmlw
The key's randomart image is:
+---[RSA 4096]----+
|+o+.+..+ .oo==+o |
|.= +.oo.=o .oo  .|
|o o oo oo.+ o . .|
|   = ... o B o . |
|  o . o S * *    |
| E     . * o     |
|        . .      |
|         .       |
|                 |
+----[SHA256]-----+
ellen@Azure:~$
```
### 1.4 Retrieve the SSH-pubkey
```
ellen@Azure:~$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCwIvmC4K/0BUwOBqCsPxw5Ht8qWyDkorrU+gc6cJbohREoMFZkMlGEe2XqIyYTpHAu0ISicZEJ4MoWExPFrrZRqoYAHPrHyNnie9tVR5UMkqzNhs31qEWLtfEBOrcJIsPNdFuvnAqiQnhMQut3Jtamjc3XnMU8kJ3yL/+xIU4vKkQ8XIey+GGowR69oJI37mYKr9jT9dejB4gP2l6JyrVehnOG6QXRtg/gzFgyX08u8wKhuohNIPLlf2VzIXQml69P9PcD3muePIxi/JsJ6hb6czMCqhyHSFA42XpUpeWTml41HuBO9R5Bcsb3Q3j4MTKQOjtssz9Dx3pwtZ+tn9mg8TLMsk9d3Ip8FVXbe9ABleutJLIYGIUcZ3GlMdnRP62Wdyzrh0VEsoCfkHjUq2qFo8Hd1j0bkR1coSr2vFZXz6my+92a8nX7dJMPH5y+DG+ZuZchBXrwy8xVSNccnqRRn1A5xKdxY5SusbUQEirYPS7oR64CH4QeH6d5iQ2p2Z2cVWYbz/DjJNoCF0Cbzp9w1KprpErlrtd1epIGQTUDgx4YhChyrdXQiYCJBJ1jvhJcWfKj6rHz94eTEr6S5W4etP/IuACqKTpmAxQqSdU7NdHZVPH8c6w89JX3DAYRjg0PKVm56Ib6C+kct8M6/NQQeyhi5DM0SGW+T7tBjDxsUQ== ellen@cc-c6733553-7b96c85fb8-hbmlw
ellen@Azure:~$
```
### 1.5 Create a service principal
```
ellen@Azure:~$ az ad sp create-for-rbac --name cyclecloudlabs
{
    "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "cyclecloudlabs",
    "name": "http://cyclecloudlabs",
    "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```
_Save this service principal somewhere. You will need it in the section below as well as in future tutorials in this lab._

### 1.6 Deploy Azure CycleCloud
[![Deploy to
Azure](https://azuredeploy.net/deploybutton.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FCycleCloudCommunity%2Fcyclecloud_arm%2Fdeploy-azure%2Fazuredeploy.json)

* Click on the button above, and you will be taken to a deploy page in the Azure
  portal ![Azure Deploy Form](images/deployment-form.png)

Enter the required information:

* *Tenant ID*: The `tenant` listed above in the service principal
* *Application ID*: `appId` of the service principal
* *Application Secret*: `password` of the service principal
* *SSH Public Key*: Copy and paste here the output of step 1.4
* *Username*: The output of step 1.2 (e.g. *johnsmith* instead of
  *johnsmith@domain.com*)

The deployment process runs an installation script as a custom script extension,
which installs and sets up CycleCloud. This process takes between 5 and 8 mins.

### 1.7 Retrieve the Domain Name (FQDN) of the Azure CycleCloud VM
* When the deployment is completed you can retrieve the fully qualified domain
  name of the Azure CycleCloud VM from the Azure portal or using the CLI in
  Cloud Shell: ![Deployment Output](images/deployment-output.png)

```
# replace ResourceGroupName with the one that you used
ellen@Azure:~$ az group deployment list -g ${ResourceGroupName} --query "[0].properties.outputs.fqdn.value"
"cyclecloud43vgp4.eastus.cloudapp.azure.com"
```

### 1.8 Logging into the Azure CycleCloud server for the first time
* In your web browser go to https://fqdn (where fqdn is the addressed retrieved
  above).
* You will be asked to enter a site name in the first screen, pick any name you
  like
* Accepting the EULA on the second screen brings you to a page that asks you to
  create a admin user.
    - Use the same `username` used above in step 1.5. Remember that this is also
      the username of your Cloud Shell session.
    - Choose a password that meets the minimum requirements. ![First
      Login](images/cc-first-login.png) ![Create
      Account](images/cc-create-account.png)

## 2.  Starting an auto-scaling HPC cluster
In this section, you will start a cluster using PBS-Pro as a scheduler, with
LAMMPS as a solver

### 2.1 Start a new LAMMPS cluster in Azure CycleCloud

If you do not have a cluster that is already running, the default start page of
Azure CycleCloud will display a wall of applications and cluster types that are
distributed with each install
* Find the LAMMPS cluster icon and select it. ![CC Cluster
  Wall](images/cc-cluster-wall.png)
* Provide a name for the new cluster and move on to the *Required Settings*
  section. ![CC New Cluster LAMMPS](images/cc-newcluster-laamps.png)
* Select a VM type that you should like to use as a `Execute VM Type`, we
  recommend the H16r if you have quota for these.
* In the networking subnet dropdown, select the subnet which has "-compute" as a
  suffix. This subnet was created as part of the ARM deployment. ![CC Cluster
  Required Settings](images/cc-cluster-required-settings.png)
* The *Advanced Settings* section allows you to configure the cluster to use a
  different OS, set up different projects, as well as to attach a public IP
  address to the cluster nodes. There is no need to change any settings here for
  the purposes of this lab. ![CC Cluster Advanced
  Settings](images/cc-cluster-adv-settings.png)
* Click the *Save* button on the bottom right-hand corner of the page to create
  and save this cluster.
* Your cluster now appears greyed-out in the cluster page. Click *Start* button
  to provision the cluster resources in Azure. ![CC Cluster
  Prepared](images/cc-cluster-prepared.png)
* Starting up the cluster for the first time takes about 10 mins for it to be
  ready. By default, only the master (or head) node of the cluster is started.
  Azure CycleCloud provisions all the necessary network and storage resources
  needed by the master node, and also sets up the scheduling environment in the
  cluster.
* The master node status bar turns green when the cluster is ready to use ![CC
  Cluster Ready](images/cc-cluster-ready.png)

### 2.2 Connecting into the master node and submitting a LAMMPS job

As part of the ARM deployment process in section 1 above, the SSH public key you
provided is stored in the Azure CycleCloud application server and pushed into
each cluster that you create. 

As a result of that, you are able to use your SSH private key to log into the
master node.

* Retrieve the public IP address of the cluster headnode by selecting the master
  node in the cluster management pane, and then clicking on the connect button
  that appears below.

* The pop-up window shows the connection string you would use to connect to the
   cluster. 

![Connect Popup](Lab1/images/connect-popup.png)


* Use your SSH client to connect to the master node. You could also use the one
   that is available in Cloud Shell:
```
ellen@Azure:~$ ssh ellen@40.114.123.148
Last login: Thu Aug  2 20:55:34 2018 from 97-113-237-75.tukw.qwest.net

 __        __  |    ___       __  |    __         __|
(___ (__| (___ |_, (__/_     (___ |_, (__) (__(_ (__|
        |

Cluster: LammpsLabs
Version: 7.5.0
Run List: recipe[cyclecloud], role[pbspro_master_role], recipe[cluster_init]
[ellen@ip-0A000404 ~]$
```

* You can verify that the job queue is empty by using the `qstat` command:
```
[ellen@ip-0A000404 ~]$ qstat -Q
Queue              Max   Tot Ena Str   Que   Run   Hld   Wat   Trn   Ext Type
---------------- ----- ----- --- --- ----- ----- ----- ----- ----- ----- ----
workq                0     0 yes yes     0     0     0     0     0     0 Exec
[ellen@ip-0A000404 ~]$
```

* Change to the demo directory, where you can find a sample LAMMPS job, and
  submit the job using existing `runpi.sh` script.
```
[ellen@ip-0A000404 ~]$ cd demo/
[ellen@ip-0A000404 demo]$ ./runpi.sh
0[].ip-0A000404
```

* Note, if you're curious, you can view the contents of the `runpi.sh` script by
  running the `cat` command. This script prepares a sample job which contains
  1000 individual tasks, and submits that job using the `qsub` command.
```
[ellen@ip-0A000404 demo]$ cat runpi.sh
#!/bin/bash
mkdir -p /shared/scratch/pi
cp ~/demo/pi.py /shared/scratch/pi
cp ~/demo/pi.sh /shared/scratch/pi
cd /shared/scratch/pi
qsub -J 1-1000 /shared/scratch/pi/pi.sh
```

* Verify that the job is now in the queue
```
[ellen@ip-0A000404 ~]$ qstat -Q
Queue              Max   Tot Ena Str   Que   Run   Hld   Wat   Trn   Ext Type
---------------- ----- ----- --- --- ----- ----- ----- ----- ----- ----- ----
workq                0     1 yes yes     1     0     0     0     0     0 Exec
[ellen@ip-0A000404 ~]$
```

* The autoscaling hook in the PBS scheduler picks up the job and submits a
  resource request to the Azure CycleCloud server. You will see nodes being
  provisioned in the Azure CycleCloud UI within a minute. ![CC Allocating
  Nodes](images/cc-allocating-nodes.ong.png) Note that CycleCloud will not
  provision more cores than the limit set on the cluster's autoscaling settings.
  In this case, the sample job contains 1000 tasks, but CycleCloud will only
  provision up to 100 cores worth of VMs. 

* After the execute nodes are provisioned, their status bars will turn green,
  and the job's tasks will start running. For non-tightly coupled jobs, where
  the individual tasks can independently execute, jobs will start running as
  soon as any VM is ready. For tightly coupled jobs (i.e. MPI jobs), jobs will
  not start executing until every VM associated with the jobs is ready.

* Verify that the job is complete by running the `qstat -Q` command
  periodically. The jobs should finish quickly, in a minute or two. 
```
[ellen@ip-0A000404 demo]$ qstat -Q
Queue              Max   Tot Ena Str   Que   Run   Hld   Wat   Trn   Ext Type
---------------- ----- ----- --- --- ----- ----- ----- ----- ----- ----- ----
workq                0     0 yes yes     0     0     0     0     0     0 Exec
```

* With no more jobs in the queue, the execute nodes will start auto-stopping,
  and your cluster will return to just having the master node.

