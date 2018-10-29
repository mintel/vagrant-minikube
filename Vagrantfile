# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
Vagrant.require_version ">= 1.6.0"

# just a single node is required
NODES = ENV['NODES'] || 1

# Memory & CPUs
MEM = ENV['MEM'] || 4096
CPUS = ENV['CPUS'] || 2

# Common installation script
$installer = <<SCRIPT
#!/bin/bash

# Update apt and get dependencies
sudo apt-get update
sudo apt-get install -y zip unzip curl wget

SCRIPT

$minikubescript = <<SCRIPT
#!/bin/bash

#Install minikube
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube 
sudo mv minikube /usr/local/bin/

#Install kubectl
curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x kubectl 
sudo mv kubectl /usr/local/bin/

#Setup minikube
mkdir -p $HOME/.minikube
mkdir -p $HOME/.kube
touch $HOME/.kube/config

export KUBECONFIG=$HOME/.kube/config

# Permissions
sudo chown -R $USER:$USER $HOME/.kube
sudo chown -R $USER:$USER $HOME/.minikube

# Start minikube 
sudo -E minikube start --vm-driver=none

SCRIPT


required_plugins = %w(vagrant-cachier vagrant-vbguest vagrant-libvirt)

required_plugins.each do |plugin|
  need_restart = false
  unless Vagrant.has_plugin? plugin
    system "vagrant plugin install #{plugin}"
    need_restart = true
  end
  exec "vagrant #{ARGV.join(' ')}" if need_restart
end


def configureVM(vmCfg, hostname, cpus, mem)

  vmCfg.vm.box = "yk0/ubuntu-xenial"
  
  vmCfg.vm.hostname = hostname
  vmCfg.vm.network "private_network", type: "dhcp"

  #Adding Vagrant-cachier
  if Vagrant.has_plugin?("vagrant-cachier")
     vmCfg.cache.scope = :machine
     vmCfg.cache.enable :apt
     vmCfg.cache.enable :gem
  end
  
  # Set resources w.r.t Virtualbox provider
  vmCfg.vm.provider "virtualbox" do |vb|
    vb.memory = mem
    vb.cpus = cpus
    vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
  end
  
   # ensure docker is installed
  vmCfg.vm.provision "docker"

  # sync your laptop's development with this Vagrant VM
  # vmCfg.vm.synced_folder '$src-dir', '$dest-dir-in-vm'

  # Script to prepare the VM
  vmCfg.vm.provision "shell", inline: $installer, privileged: false 
  vmCfg.vm.provision "shell", inline: $minikubescript, privileged: false

  return vmCfg
end

# Entry point of this Vagrantfile
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # I do not want this
  config.vbguest.auto_update = false
  
  1.upto(NODES.to_i) do |i|
    hostname = "minikube-vagrant-%02d" % [i]
    cpus = CPUS
    mem = MEM
    
    config.vm.define hostname do |vmCfg|
      vmCfg = configureVM(vmCfg, hostname, cpus, mem)  
    end
  end

end
