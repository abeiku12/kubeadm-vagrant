This README provides setup instructions for using Vagrant to provision a local Kubernetes cluster. The configuration file (Vagrantfile) sets up a 3-node cluster with 1 master node and 2 worker nodes, allowing you to run a Kubernetes cluster entirely on your local machine.

Prerequisites
Before running this setup, make sure you have the following installed on your machine:

Vagrant - For managing virtual environments
VirtualBox - The VM provider for creating virtual machines
Setup Overview
The Vagrantfile provisions a Kubernetes cluster with:

1 master node (master)
2 worker nodes (worker1 and worker2)
Flannel as the network plugin
The nodes are provisioned with Ubuntu 18.04 and use kubeadm to set up and initialize the Kubernetes cluster.

Steps to Set Up the Cluster
Clone or Download the Repository:
Clone or download the project files to your local machine.

Start the Cluster: Open a terminal, navigate to the directory with the Vagrantfile, and run:

bash:
Copy code
vagrant up
This command will:

Create and configure three virtual machines.
Set up Kubernetes on each node.
Initialize a Kubernetes master node on master.
Join the worker nodes (worker1 and worker2) to the cluster.
Access the Master Node: SSH into the master node to start interacting with the Kubernetes cluster:

bash:
Copy code
vagrant ssh master
Verify Cluster Status: Once inside the master node, you can check the cluster status by running:

bash:
Copy code
kubectl get nodes
kubectl get pods -A
You should see all three nodes listed as "Ready" and Flannel pods running as the network plugin.

Key Files and Configuration
Vagrantfile
The Vagrantfile:

Defines three virtual machines: master, worker1, and worker2.
Installs kubeadm, kubelet, and kubectl on each node.
Initializes the Kubernetes master node with kubeadm.
Sets up Flannel as the pod network plugin.
Configures worker nodes to join the cluster automatically.
Cluster Networking
The cluster uses a private network (private_network) for node communication. This allows the virtual machines to communicate internally without being exposed to external networks. You can customize the networking options in the Vagrantfile if needed.

Customizing the Setup
Feel free to customize the Vagrantfile to adjust:

Number of nodes
VM resources (memory, CPU)
Network plugins (e.g., switch from Flannel to Calico)
Stopping and Destroying the Cluster
To stop the cluster without destroying it:

bash:
Copy code
vagrant halt
To destroy the cluster completely:

bash:
Copy code
vagrant destroy -f
Troubleshooting
If you encounter issues with the setup, make sure VirtualBox and Vagrant are up-to-date.
Check for any errors in the provisioning script output during vagrant up.
If worker nodes don’t join automatically, you can manually SSH into each worker node and run the join.sh script saved in /vagrant/join.sh on the master node.

Flannel Installation
To install Flannel as the network plugin in a Kubernetes cluster, follow these steps. Flannel is a popular network overlay for Kubernetes that enables communication between pods across nodes.

Prerequisites
A Kubernetes cluster initialized with kubeadm on the master node.
kubectl configured on the master node (or locally) to manage the cluster.
Initialize the Kubernetes Cluster (if not already done): If you haven't already initialized the Kubernetes cluster, you can do so with kubeadm. For example:

bash:
Copy code
kubeadm init --pod-network-cidr=10.244.0.0/16
Note: The --pod-network-cidr=10.244.0.0/16 flag is required for Flannel, as Flannel expects this CIDR. Adjustments to this CIDR should be made only if you’re certain your setup needs it.

Apply the Flannel Network Manifest: Run the following command on the master node to deploy Flannel using the official Flannel YAML manifest:

bash:
Copy code
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
Verify the Installation: After applying the manifest, check that the Flannel pods are up and running. This may take a few moments.

bash:
Copy code
kubectl get pods -n kube-system
You should see kube-flannel-ds pods running on each node in the cluster.

Allow Workers to Communicate (if applicable): If you initialized the cluster with kubeadm init, make sure you have joined your worker nodes to the cluster. The command to join worker nodes will typically look like:

bash:
Copy code
kubeadm join <master-node-ip>:<port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>
If you forgot or lost the join command, you can regenerate it on the master node:

bash:
Copy code
kubeadm token create --print-join-command
Confirm Pod-to-Pod Communication: Deploy a sample application to confirm pod networking is working. You can create a simple nginx deployment and check connectivity between pods:

bash:
Copy code
kubectl create deployment nginx --image=nginx
kubectl get pods -o wide
If Flannel is configured correctly, pods should be able to communicate across nodes within the cluster.
