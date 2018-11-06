# Minikube Vagrant

Provide a consistent way to run minikube locally across different distro's..

Mostly used for demo's, tutorials and workshops. If you are using minikube for day to day tasks, install it using your package manager instead (and avoid the extra overhead of Vagrant).

Note, this installs the latest `minikube` and `kubectl` packages available for ubuntu 16.04.

## Install Pre-requisites

Ensure you have vagrant installed (should also support mac/windows)

https://www.vagrantup.com/docs/installation/

### Arch
```
sudo pacman -S vagrant
```

### Ubuntu
```
sudo apt-get install vagrant
```

## Run it

Clone this repo then:

```
vagrant up
```

## SSH into the VM
```
vagrant ssh
```

## Check minikube is up and running

```
kubectl get nodes
```

## Access your code inside the VM

We automatically mount `/tmp/vagrant` into `/home/vagrant/data`.

For example, you may want to `git clone` some kubernetes manifests into `/tmp/vagrant` on your host-machine, then you can access them in the vagrant machine.

This is bi-directional, and achieved via [vagrant-sshfs](https://github.com/dustymabe/vagrant-sshfs)