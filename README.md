Vagrant Kubernetes Cluster Setup
This repository provides a Vagrant script for setting up a simple multi-node Kubernetes cluster using VirtualBox as the provider. The setup includes one master node and two worker nodes, all running Ubuntu 18.04 (Bionic). The Kubernetes components (kubeadm, kubelet, kubectl) and Docker are installed and configured on all nodes.

Prerequisites
Before running this script, ensure you have the following software installed on your system:

Vagrant (https://www.vagrantup.com/)
VirtualBox (https://www.virtualbox.org/)
Git (optional, for cloning the repository)
System Requirements
At least 4 GB of available RAM for the virtual machines.
A system with virtualization support (Intel VT-x or AMD-V) enabled in BIOS.
Configuration Overview
The Vagrant script sets up the following virtual machines:

k8s-master: The Kubernetes master node, responsible for the control plane.
k8s-node1: The first Kubernetes worker node.
k8s-node2: The second Kubernetes worker node.
Virtual Machine Configuration
Base Box: ubuntu/bionic64 (Ubuntu 18.04)
Memory: 2048 MB per VM
CPUs: 2 CPUs per VM
Networking:
Private network IPs:
k8s-master: 192.168.56.10
k8s-node1: 192.168.56.11
k8s-node2: 192.168.56.12
Public network interface bridged to Wi-Fi (en0).
Setup Instructions
1. Clone the Repository
 bash
Copy code
git clone <repository_url>
cd <repository_directory>
2. Start the Vagrant Environment
Run the following command to bring up the VMs:

 bash
vagrant up
Vagrant will automatically download the base Ubuntu box, create virtual machines, and execute provisioning scripts to set up Docker and Kubernetes on each node.

3. Accessing the VMs
To SSH into the master node (k8s-master):

 bash

vagrant ssh k8s-master
To SSH into the first worker node (k8s-node1):

 bash

vagrant ssh k8s-node1
To SSH into the second worker node (k8s-node2):

 bash
 
vagrant ssh k8s-node2
4. Kubernetes Initialization
Once the nodes are up and running, the master node will automatically initialize the Kubernetes cluster using kubeadm. The following steps are included in the provisioning script:

Kubernetes components (kubelet, kubeadm, kubectl) are installed on all nodes.
Docker is installed and configured.
The swap is disabled on all nodes.
Flannel, a network plugin for Kubernetes, is installed to enable pod-to-pod communication.
After kubeadm init completes on the master node, you will see a kubeadm join command in the output, which you can use to join the worker nodes to the cluster.

5. Joining Worker Nodes to the Cluster
Run the kubeadm join command provided by the master node on each worker node (k8s-node1 and k8s-node2) to join them to the cluster.

6. Accessing the Kubernetes Cluster
After the nodes are joined to the cluster, you can use kubectl to manage the cluster. To use kubectl from the master node, run the following:

 bash
 
export KUBEVIRT_VERSION=$(kubectl version | base64 | tr -d '\n')
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
You can then verify the cluster status:

 bash
kubectl get nodes or kubectl get po -n kube-system
This should show the master and worker nodes as Ready.

Troubleshooting
If any of the VMs fail to start, run vagrant destroy followed by vagrant up to recreate the environment.
Ensure your system has sufficient resources (RAM and CPU) for running multiple VMs.
If you experience networking issues, ensure that VirtualBox's network interface settings are correctly configured.
Unused Features or Considerations
This setup uses Flannel as the network plugin, but you can replace it with other CNI (Container Network Interface) plugins if required.
This script uses Ubuntu 18.04 (Bionic) as the base image. It can be adapted for other Ubuntu versions or different Linux distributions.
License
This project is licensed under the MIT License - see the LICENSE file for details.



Flannel Installation
To install Flannel as the network plugin in a Kubernetes cluster, follow these steps. Flannel is a popular network overlay for Kubernetes that enables communication between pods across nodes.

Prerequisites
A Kubernetes cluster initialized with kubeadm on the master node.
kubectl configured on the master node (or locally) to manage the cluster.
Initialize the Kubernetes Cluster (if not already done): If you haven't already initialized the Kubernetes cluster, you can do so with kubeadm. For example:

bash:

kubeadm init --pod-network-cidr=10.244.0.0/16
Note: The --pod-network-cidr=10.244.0.0/16 flag is required for Flannel, as Flannel expects this CIDR. Adjustments to this CIDR should be made only if you’re certain your setup needs it.

Apply the Flannel Network Manifest: Run the following command on the master node to deploy Flannel using the official Flannel YAML manifest:

bash:

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
Verify the Installation: After applying the manifest, check that the Flannel pods are up and running. This may take a few moments.

bash:

kubectl get pods -n kube-system
You should see kube-flannel-ds pods running on each node in the cluster.

Allow Workers to Communicate (if applicable): If you initialized the cluster with kubeadm init, make sure you have joined your worker nodes to the cluster. The command to join worker nodes will typically look like:

bash:

kubeadm join <master-node-ip>:<port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>
If you forgot or lost the join command, you can regenerate it on the master node:

bash:

kubeadm token create --print-join-command
Confirm Pod-to-Pod Communication: Deploy a sample application to confirm pod networking is working. You can create a simple nginx deployment and check connectivity between pods:

bash:

kubectl create deployment nginx --image=nginx
kubectl get pods -o wide
If Flannel is configured correctly, pods should be able to communicate across nodes within the cluster.
