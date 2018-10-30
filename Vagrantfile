# -*- mode: ruby -*-
# vi: set ft=ruby :
require 'etc'

VAGRANTFILE_API_VERSION = "2"
Vagrant.require_version ">= 2.0.0"

# just a single node is required
NODES = ENV['NODES'] || 1

# Memory & CPUs
MEM = ENV['MEM'] || 4096
CPUS = ENV['CPUS'] || 2

# 9p Mount
UID = Etc.getpwnam(ENV['USER']).uid 
#SRCDIR = ENV['SRCDIR'] || "/home/"+ENV['USER']+"/test"
SRCDIR = ENV['SRCDIR'] || "/dev/shm/test"
DSTDIR = ENV['DSTDIR'] || "/home/vagrant/data"


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


required_plugins = %w(vagrant-libvirt)

required_plugins.each do |plugin|
  need_restart = false
  unless Vagrant.has_plugin? plugin
    system "vagrant plugin install #{plugin}"
    need_restart = true
  end
  exec "vagrant #{ARGV.join(' ')}" if need_restart
end


def configureVM(vmCfg, hostname, cpus, mem, uid, srcdir, dstdir)

  vmCfg.vm.box = "roboxes/ubuntu1804"
  
  vmCfg.vm.hostname = hostname
  vmCfg.vm.network "private_network", type: "dhcp",  :model_type => "virtio"

  # First and only Provider - Vagrant will default to this unless overwritten on CLI 
  vmCfg.vm.provider "libvirt" do |provider|
    provider.memory = mem
    provider.cpus = cpus
    provider.driver = "kvm"
    provider.disk_bus = "virtio"
    provider.machine_virtual_size = 64
  end
  

  vmCfg.vm.synced_folder '.', '/vagrant', disabled: true
  # sync your laptop's development with this Vagrant VM
  vmCfg.vm.synced_folder srcdir, dstdir, type: '9p', accessmode: "mapped", disabled: false, owner: uid

  # ensure docker is installed
  #vmCfg.vm.provision "docker"
  # Script to prepare the VM
  #vmCfg.vm.provision "shell", inline: $installer, privileged: false 
  #vmCfg.vm.provision "shell", inline: $minikubescript, privileged: false

  return vmCfg
end

# Entry point of this Vagrantfile
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  1.upto(NODES.to_i) do |i|
    hostname = "minikube-vagrant-%02d" % [i]
    cpus = CPUS
    mem = MEM
    uid = UID
    srcdir = SRCDIR
    dstdir = DSTDIR
    
    config.vm.define hostname do |vmCfg|
      vmCfg = configureVM(vmCfg, hostname, cpus, mem, uid, srcdir, dstdir)  
    end
  end

end
