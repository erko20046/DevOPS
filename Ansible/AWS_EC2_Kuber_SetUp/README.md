# **Kubernetes Cluster Setup Documentation**

# **Introduction**

This document provides a professional and detailed guide for setting up a Kubernetes cluster using Ansible automation. It includes the cluster architecture, configuration details, and step-by-step instructions for deploying and managing the cluster. This documentation is intended for system administrators and DevOps engineers.

---

# **Cluster Architecture**

**Node Overview**

The Kubernetes cluster consists of the following nodes:

| **Node Name** | **Role** |
| --- | --- |
| kuber-master | Control Plane |
| kuber-node-1 | Master |
| kuber-node-2 | Worker |
| kuber-node-3 | Worker |

**Cluster Networking**

- **Pod CIDR**: 10.244.0.0/16
- **Network Plugin**: Calico

---

# **Ansible Directory Structure**

The Ansible directory contains the following files and playbooks:

| **File/Directory** | **Description** |
| --- | --- |
| inventory.ini | Inventory file defining the nodes and their roles. |
| setup_hosts.yml | Configures hosts on all nodes. |
| disable_swap.yml | Disables swap on all nodes. |
| set_hostname.yml | Sets hostnames for master and worker nodes. |
| enable_networking.yml | Configures kernel modules and sysctl parameters for networking. |
| firewalld.yml | Configures firewall rules for Kubernetes. |
| install_containerd.yml | Installs and configures the container runtime (Containerd). |
| install_kubernetes.yml | Installs Kubernetes components (kubeadm, kubelet, kubectl). |
| kubeadm_init.yml | Initializes the Kubernetes control plane on the master node. |
| install_calico.yml | Deploys the Calico network plugin. |
| join_workers.yml | Joins worker nodes to the Kubernetes cluster. |

---

# **Setup Steps**

**1. Configure Hostnames**

- **Playbook**: set_hostname.yml
- **Description**: Sets the hostname for each node based on its role.
- **Configuration Path**: /etc/hostname

**2. Update Hosts**

- **Playbook**: setup_hosts.yml
- **Description**: Adds the IP addresses and hostnames of all nodes to hosts.
- **Configuration Path**: hosts

**3. Disable Swap**

- **Playbook**: disable_swap.yml
- **Description**: Disables swap temporarily and removes swap entries from /etc/fstab.
- **Configuration Path**: /etc/fstab

**4. Enable Networking**

- **Playbook**: enable_networking.yml
- **Description**: Loads required kernel modules and configures sysctl parameters for networking.
- **Configuration Paths**:
    - /etc/modules-load.d/k8s.conf
    - /etc/sysctl.d/k8s.conf

**5. Configure Firewall**

- **Playbook**: firewalld.yml
- **Description**: Opens required ports for Kubernetes on master and worker nodes.
- **Ports**:
    - Master: 6443, 2379-2380, 10250, 10251, 10252, 10255
    - Workers: 10250, 30000-32767

**6. Install Containerd**

- **Playbook**: install_containerd.yml
- **Description**: Installs and configures Containerd as the container runtime.
- **Configuration Path**: /etc/containerd/config.toml

**7. Install Kubernetes Components**

- **Playbook**: install_kubernetes.yml
- **Description**: Installs kubeadm, kubelet, and kubectl on all nodes.
- **Configuration Path**: /etc/yum.repos.d/kubernetes.repo

**8. Initialize Kubernetes Control Plane**

- **Playbook**: kubeadm_init.yml
- **Description**: Initializes the Kubernetes control plane on the master node.
- **Configuration Path**: $HOME/.kube/config

**9. Deploy Calico Network Plugin**

- **Playbook**: install_calico.yml
- **Description**: Deploys the Calico network plugin for pod networking.
- **Configuration File**: custom-resources.yaml (modified for Pod CIDR 10.244.0.0/16).

**10. Join Worker Nodes**

- **Playbook**: join_workers.yml
- **Description**: Executes the kubeadm join command on worker nodes to join them to the cluster.

---

# **Configuration Files and Paths**

| **File/Directory** | **Description** |
| --- | --- |
| hosts | Hosts file updated with node IPs and hostnames. |
| /etc/fstab | Swap entries removed to disable swap. |
| /etc/modules-load.d/k8s.conf | Kernel modules required for Kubernetes. |
| /etc/sysctl.d/k8s.conf | Networking parameters for Kubernetes. |
| /etc/containerd/config.toml | Containerd configuration file. |
| /etc/yum.repos.d/kubernetes.repo | Kubernetes repository configuration. |
| $HOME/.kube/config | Kubeconfig file for the master node. |