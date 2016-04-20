# -*- mode: ruby -*-
# vi: set ft=ruby :

DOCKER_HOSTS=3
Vagrant.configure(2) do |vagrant|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  vagrant.vm.box = "centos/7"

  # VirtualBox Configuration
  vagrant.vm.provider "virtualbox" do |provider, override|
    override.vm.box = "centos/7"
    # override.vm.box_url = 'https://artifcatory.internal/centos.box'
  end
  vagrant.vm.provider "virtualbox" do |v|
    v.memory = 1024
    v.cpus = 1
  end


  # Machine Configuration
  # Docker Hosts
  (1..DOCKER_HOSTS).each do |i|
    docker_host = i.to_s.rjust(2, "0")
    vagrant.vm.define "dockerhost#{docker_host}" do |config|
      config.vm.hostname = "dockerhost#{docker_host}"
      config.vm.network "private_network", ip: "10.100.199.2#{docker_host}"
      config.vm.provision :hosts
      config.vm.provision :ansible do |ansible|
        ansible.playbook = 'ansible/vagrant_docker_host.yml'
        ansible.groups   = {'vagrant_dockerhosts' => ["dockerhost#{docker_host}"], 'local' => ['localhost']}
        ansible.raw_arguments = '--timeout=30'
        ansible.host_key_checking = false
      end
    end
  end

  # Swarm Host
  vagrant.vm.define "dockerswarm01" do |config|
    config.vm.hostname = "dockerswarm01"
    config.vm.network "private_network", ip: "10.100.199.200"
    config.vm.network "forwarded_port", guest: 2376, host: 2376
    config.vm.network "forwarded_port", guest: 2375, host: 2375
    config.vm.provision :hosts
    config.vm.provision :ansible do |ansible|
      ansible.playbook = 'ansible/vagrant_docker_swarm.yml'
      ansible.groups   = {'vagrant_dockerswarm' => ["dockerswarm01"], 'local' => ['localhost']}
      ansible.raw_arguments = '--timeout=30'
      ansible.host_key_checking = false
    end
  end
end
