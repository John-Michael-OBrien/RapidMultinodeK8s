# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

# Provisioning script for the administration computer which makes
# auth keys, copies the public key into the shared folder for the clients
# to import. And installs ansible.
$baseprovision = <<-SCRIPT
     apt-get update
SCRIPT

$admprovision = <<-SCRIPT
     echo Generating Identity
     sudo -u vagrant ssh-keygen -N "" -q -t rsa -f /home/vagrant/.ssh/id_rsa
	 cp /home/vagrant/.ssh/id_rsa ~/.ssh/id_rsa
     echo "Exporting Administrator Public Key..."
     cp /home/vagrant/.ssh/id_rsa.pub /vagrant/adm_id_rsa.pub

	 echo "Installing Ansible..."
     apt-get install software-properties-common
     apt-add-repository ppa:ansible/ansible
     apt-get update
	 apt-get install -y ansible
 	 apt-get install -y python-pip

	 cd /vagrant/Provision
	 cp hosts /etc/ansible/hosts
	 
	 cd ~
	 git clone https://github.com/kubernetes-incubator/kubespray.git
	 cd kubespray
	 pip install -r requirements.txt
	 cp -r /vagrant/jmcluster inventory/jmcluster
SCRIPT
#	 ansible-galaxy install geerlingguy.docker
#	 ansible-galaxy install geerlingguy.pip
#	 ansible-galaxy install geerlingguy.kubernetes	 

# Import the public key from the administrator into our authorized_keys.
$workerprovision = <<-SCRIPT
echo "Importing Administrator Public Key..."
sudo -u vagrant cat  /vagrant/adm_id_rsa.pub >> /home/vagrant/.ssh/authorized_keys 
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

  config.vm.provision "shell", inline: $baseprovision 
  
  config.vm.define "master" do |master|
     master.vm.network "private_network", ip: "10.10.77.2", netmask: "24"
	 master.vm.hostname = "kubely-master"
     master.vm.provider "virtualbox" do |vb|
       vb.memory = "4096"
     end
     master.vm.provision "shell", inline: $admprovision
  end

  config.vm.define "worker" do |worker|
     worker.vm.network "private_network", ip: "10.10.77.3", netmask: "24"
	 worker.vm.hostname = "kubely-worker1"
     worker.vm.provider "virtualbox" do |vb|
       vb.memory = "4096"
     end
     worker.vm.provision "shell", inline: $workerprovision
  end 
  
  config.vm.provision "shell", inline: <<-SHELL
  SHELL
end