$script = <<-SCRIPT
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
sudo apt-get update
sudo apt-get install -yqq \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -yqq docker-ce docker-ce-cli containerd.io
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -yqq kubelet=1.20.1-00 kubeadm=1.20.1-00  kubectl=1.20.1-00
sudo apt-mark hold kubelet kubeadm kubectl

echo '
/dev/mapper/vagrant--vg-root /               ext4    errors=remount-ro 0       1
/vagrant /vagrant vboxsf uid=1000,gid=1000,nofail 0 0
' > /etc/fstab

swapoff -a


echo '
127.0.0.1       localhost
127.0.1.1       vagrant.vm      vagrant

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.20.30.10 master1
10.20.30.11 worker1
10.20.30.12 worker2
' > /etc/hosts
SCRIPT



Vagrant.configure("2") do |config|
    config.vm.provision "shell", inline: $script
    config.vm.box = "bento/ubuntu-18.04"
	config.vm.define "master1" do |master1|
		master1.vm.hostname = "master1"
		master1.vm.network "private_network", ip: "10.20.30.10"
		master1.vm.provider "virtualbox" do |v|
			v.memory = 3096
			v.cpus = 2
			v.name = "master1"
		end
	end
	config.vm.define "worker1" do |worker1|
		worker1.vm.hostname = "worker1"
		worker1.vm.network "private_network", ip: "10.20.30.11"
		worker1.vm.provider "virtualbox" do |v|
			v.memory = 3096
			v.cpus = 2
			v.name = "worker1"
		end
	end
	config.vm.define "worker2" do |worker2|
		worker2.vm.hostname = "worker2"
		worker2.vm.network "private_network", ip: "10.20.30.12"
		worker2.vm.provider "virtualbox" do |v|
			v.memory = 3096
			v.cpus = 2
			v.name = "worker2"
		end
	end
end
