# Autok8s (Automatic Kubernetes)

The idea behind this project is to fully automate the installation of a Kubernetes cluster on bare-metal or virtual machines. The primary target audience for this is k8s nerds that want a full cluster at home for free, but this may also help those looking to create an on-premis or entirely spot instance cluster in a professional environment.

Unlike managed Kubernetes services such as EKS, AKS or GKE, the control-plane (master) node is something that you have to setup yourself. Cloud providers abstract this away from you. This script fully automates the installation and configuration of Docker, Kubernetes and various Helm charts for networking, load balancers and storage on the master node, as well as the worker nodes, via bash scripts.

There are plenty of great articles out there on how to do this, but the process is not arbitrary. It can take a long time to get working and involves quite a bit of manual work. 

The idea of Autok8s is to run a script, wait 15ish minutes, and have a fully functional and ready to go Kubernetes cluster, just as you would have in the cloud.

---

There will be a separate set of scripts for those wanting to make use of the vSphere CPI and CSI drivers, and possibly in the future other drivers or hardware/environments as well. 

This project is very early days and thus far has only been tested on a fresh installation of Ubuntu 20.04 on an older VMware hypervisor (without the CPI and CSI drivers). In the future more testing will be performed to ensure it works as expected in any environment. If you do run this script on a different environment, please let me know your findings and feel free to create pull requests.

---

It's highly recommended that you run this on a brand new Ubuntu 20.04 VM. When you install the Ubuntu OS and are presented with the list of optional packages to install, DO NOT select docker. The version of docker installed will be `docker.io` which is no longer compatible with Kubernetes. This script will install `docker-ce` for you instead.

A high-level overview of the steps `setup_aks_master.sh` will perform:

- Configure TCP/IP settings including DNS servers and search domains. (Optional. Added for convenience and time saving).

- Installs prerequisite packages such as `apt-transport-https`, `ca-certificates`, etc.  

- Adds Docker and Kubernetes repositories.

- Installs Docker CE and containerd, then applies required configuration for Kubernetes.

- Installs Kubernetes packages

- Initializes Kubernetes with the `kubeadm` command.

- Creates the `~./kube/config` files so you can use `kubectl` as soon as its finished.

- Installs Flannel layer 3 networking.

- Installs Helm

- Installs NFS file server on the host, NFS CSI drivers and adds storage class.

    (This is entirely optional and not recommended for production use. It's mainly for those that want a working storage solution out of the box.)

- Installs SMB file server on the host, SMB CSI drivers and adds storage class.

    (Again this optional. You can also specify an existing server SMB and/or NFS server to use rather than make the master node a file server.)

- Installs MetalLB (For use with home or on-premis networks. Requires that you reserve a range of IP addresses on your local network to be use by Kubernetes service objects.)

- Prints a friendly success message at the end with tips on how to connect to your cluster and how to add worker nodes.

---

Within the scripts are a collection of User Variables which you will need to set before running them. If you run this on a VM and have the ability to take a snapshot before you start, it is recommended you do so because if the script fails or if you want to do it again with different options then running the script more than once may have unexpected results.

Also within the script you will find URLs for documentation and sources relating to the various tasks which you can read to gain understanding of what's being installed, why, and what other configuration options you may want.

---

Once your master node is up and running you can use the apply manifests found in the [TestManifests/Storage](https://github.com/7wingfly/autok8s/tree/main/TestManifests/Storage) directory to test out NFS and SMB and storage. In future other testing manifests will be added.


---

Links to documentation used to create this project:

Docker install docs:
<br>
https://docs.docker.com/engine/install/ubuntu/

Containerd config:
<br>
https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd-systemd

Kubernetes install docs:
<br>
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

Kubeadm init docs:
<br>
https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/


Flannel networking docs:
<br>
https://github.com/flannel-io/flannel/#readme

NFS CSI driver:
<br>
https://github.com/kubernetes-csi/csi-driver-nfs
<br>
https://github.com/kubernetes-csi/csi-driver-nfs/tree/master/charts

SMB CSI driver:
<br>
https://github.com/kubernetes-csi/csi-driver-smb
<br>
https://github.com/kubernetes-csi/csi-driver-smb/tree/master/charts
<br>
https://ubuntu.com/tutorials/install-and-configure-samba#2-installing-samba


MetalLB load balancer 
<br>
https://metallb.universe.tf/installation/
<br>
https://metallb.universe.tf/configuration/_advanced_l2_configuration/
