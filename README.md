# Minikube Vagrant

Provide a consistent way to run minikube locally across different distro's..

Mostly used for demo's, tutorials and workshops. If you are using minikube for day to day tasks, install it using your package manager.

## Install

Ensure you have vagrant installed.

### Arch
```
sudo pacman -S vagrant 
```

### Ubuntu 
```
sudo apt-get install vagrant
```

## Run it

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

You can sync. folders - see the following:
- https://www.vagrantup.com/docs/synced-folders/
- https://www.vagrantup.com/docs/synced-folders/basic_usage.html

By default, Vagrant will share your project directory (the directory with the Vagrantfile) to /vagrant.

Therefore, put any code you want to access in the VM into `/vagrant` ;)

## Known Issues

- Can't use ubuntu 18.04 vagrant image due to using `upstart` instead of `systemd` (minikube fails)
- Sometimes hit this issue on arch https://github.com/hashicorp/vagrant/issues/9666