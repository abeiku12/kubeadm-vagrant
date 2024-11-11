Vagrant.configure("2") do |config|
  # Define the base box to use for Ubuntu
  config.vm.box = "ubuntu/bionic64"

  # Common configuration for all nodes
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
  end

  # Define k8s-master Node
  config.vm.define "k8s-master" do |k8smaster|
    k8smaster.vm.hostname = "k8s-master"
    
    # Configure private and public networks
    k8smaster.vm.network "private_network", ip: "192.168.56.10"
    k8smaster.vm.network "public_network", bridge: "en0: Wi-Fi (Wireless)"
    
    # Provisioning steps for Kubernetes master node
    k8smaster.vm.provision "shell", inline: <<-SHELL
      # Disable swap
      sudo swapoff -a
      
      # Install Docker
      sudo apt-get update
      sudo apt-get install -y docker.io
      sudo systemctl start docker
      sudo systemctl enable docker
      
      # Install Kubernetes components
      sudo apt-get update
      sudo apt-get install -y apt-transport-https ca-certificates curl gpg
      sudo mkdir -p -m 755 /etc/apt/keyrings
      curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
      echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
      
      # Install kubelet, kubeadm, and kubectl
      sudo apt-get update
      sudo apt-get install -y kubelet kubeadm kubectl
      sudo apt-mark hold kubelet kubeadm kubectl
      
      # Enable kubelet service
      sudo systemctl enable --now kubelet
      

    SHELL
  end

  # Define k8s-node1 Node
  config.vm.define "k8s-node1" do |k8s_node1|
    k8s_node1.vm.hostname = "k8s-node1"
    k8s_node1.vm.network "private_network", ip: "192.168.56.11"
    k8s_node1.vm.network "public_network", bridge: "en0: Wi-Fi (Wireless)"
    
    # Provisioning steps for Kubernetes worker node
    k8s_node1.vm.provision "shell", inline: <<-SHELL
      # Disable swap
      sudo swapoff -a
      
      # Install Docker
      sudo apt-get update
      sudo apt-get install -y docker.io
      sudo systemctl start docker
      sudo systemctl enable docker
      
      # Install Kubernetes components
      sudo apt-get update
      sudo apt-get install -y apt-transport-https ca-certificates curl gpg
      
      # Add Kubernetes apt repository
      sudo mkdir -p -m 755 /etc/apt/keyrings
      curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg 
      echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
      
      # Install kubelet, kubeadm, and kubectl
      sudo apt-get update
      sudo apt-get install -y kubelet kubeadm kubectl
      sudo apt-mark hold kubelet kubeadm kubectl
      
      # Enable and start kubelet
      sudo systemctl enable --now kubelet
      
      
    SHELL
  end

  # Define k8s-node2 Node
  config.vm.define "k8s-node2" do |k8s_node2|
    k8s_node2.vm.hostname = "k8s-node2"
    k8s_node2.vm.network "private_network", ip: "192.168.56.12"
    k8s_node2.vm.network "public_network", bridge: "en0: Wi-Fi (Wireless)"
    
    # Provisioning steps for Kubernetes worker node
    k8s_node2.vm.provision "shell", inline: <<-SHELL
      # Disable swap
      sudo swapoff -a
      
      # Install Docker
      sudo apt-get update
      sudo apt-get install -y docker.io
      sudo systemctl start docker
      sudo systemctl enable docker

      # Install Kubernetes components
      sudo apt-get update
      sudo apt-get install -y apt-transport-https ca-certificates curl gpg

      # Add Kubernetes apt repository
	  sudo mkdir -p -m 755 /etc/apt/keyrings
      curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg 
      echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

      # Install kubelet, kubeadm, and kubectl
      sudo apt-get update
      sudo apt-get install -y kubelet kubeadm kubectl
      sudo apt-mark hold kubelet kubeadm kubectl

      # Enable and start kubelet
      sudo systemctl enable --now kubelet

      
	  SHELL
  end
end
