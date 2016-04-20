# seismic-motivation
Running driven and ES on vagrant. 

This project aims to be a complete demonstrator of how you would run driven [http://driven.io] in production. 

Everything is running on vagrant a docker.

It is supposed to run one vagrant VM which will run docker and the consul container.

The rest of the vagrant VMs are participating in the swarm and they are running the following containers: 
- hipache as reverse proxy
- driven app containers
- elastic search containers

### Prerequisits
####VirtualBox 5.0.x

####Vagrant
```
$ vagrant version
Installed Version: 1.8.1
Latest Version: 1.8.1
 
You're running an up-to-date version of Vagrant!
```

####Vagrant hosts plugin
```
$ vagrant plugin install vagrant-hosts
```

####Docker
```
$ docker -v
Docker version 1.10.3, build 20f81dd
```

####Ansible 
```
$ pip install ansible
$ ansible --version
ansible 2.0.1.0
  config file = 
  configured module search path = Default w/o overrides
```

### Running
```
$ vagrant up
```
