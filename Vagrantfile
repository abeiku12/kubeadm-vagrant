Vagrant.configure("2") do |config|
  # Define the base box to use for Ubuntu
  config.vm.box = "ubuntu/bionic64"

  # Common configuration for all nodes
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
  end

  # Master Node
  config.vm.define "master" do |master|
    master.vm.hostname = "k8s-master"
	master.vm.network "public_network", bridge: "en0: Wi-Fi (Wireless)"
    master.vm.network "private_network", ip: "192.68.56.10"
    master.vm.provision "shell", inline: <<-SHELL
sudo swapoff -a
sudo apt-get update
sudo apt-get install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo systemctl enable --now kubelet

       SHELL
  end

  # Worker Node 1
  config.vm.define "worker1" do |worker|
    worker.vm.hostname = "k8s-worker1"
	worker.vm.network "public_network", bridge: "en0: Wi-Fi (Wireless)"
    worker.vm.network "private_network", ip: "192.68.56.11"
    worker.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
sudo apt-get install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo systemctl enable --now kubelet
    SHELL
  end

  # Worker Node 2
  config.vm.define "worker2" do |worker|
    worker.vm.hostname = "k8s-worker2"
	worker.vm.network "public_network", bridge: "en0: Wi-Fi (Wireless)"
    worker.vm.network "private_network", ip: "192.68.56.12"
    worker.vm.provision "shell", inline: <<-SHELL
     sudo apt-get update
sudo apt-get install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo systemctl enable --now kubelet
    SHELL
  end
end
