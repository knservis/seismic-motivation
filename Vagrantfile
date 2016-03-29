# -*- mode: ruby -*-
# vi: set ft=ruby :
DOCKER_MANAGER_HOSTS = 2
DOCKER_WORKER_HOSTS = 3

Vagrant.configure(2) do |vagrant|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  vagrant.vm.box = 'centos/7'

  # VirtualBox Configuration
  vagrant.vm.provider 'virtualbox' do |provider, override|
    override.vm.box = 'centos/7'
    # override.vm.box_url = 'https://artifcatory.internal/centos.box'
  end
  vagrant.vm.provider 'virtualbox' do |v|
    v.memory = 1024
    v.cpus = 1
  end


  # Machine Configuration
    
  consul_hostname = 'dockerconsul01' 
  consul_host_ip = "10.100.199.200"
  consul_host_list = [{
      'host_ip' => consul_host_ip,
      'hostname'=> consul_hostname,
      'ansible_playbook' => 'ansible/vagrant_docker_consul.yml',
      'ansible_group' => 'vagrant_dockerconsul',
      'ports' => []
  }]

  manager_host_list = (1..DOCKER_MANAGER_HOSTS).map do |i|
    docker_host_number = i.to_s.rjust(2, "0")
    {
      'host_ip' => "10.100.199.2#{docker_host_number}",
      'hostname'=> "dockerhost#{docker_host_number}",
      'ansible_playbook' => 'ansible/vagrant_docker_master.yml',
      'ansible_group' => 'vagrant_docker_manager_hosts',
      'ports' => []
    }
  end

  worker_host_list = (DOCKER_MANAGER_HOSTS+1..DOCKER_MANAGER_HOSTS+DOCKER_WORKER_HOSTS).map do |i|
    docker_host_number = i.to_s.rjust(2, "0")
    {
      'host_ip' => "10.100.199.2#{docker_host_number}",
      'hostname'=> "dockerhost#{docker_host_number}",
      'ansible_playbook' => 'ansible/vagrant_docker_worker.yml',
      'ansible_group' => 'vagrant_docker_worker_hosts',
      'ports' => []
    }
  end
  
  

  #Ansible inventory (TODO: can be improved by using the relevant keys from the lists above to keep DRY)
  ansible_groups = {
    'vagrant_dockerconsul' => consul_host_list.map { |h| h['hostname'] }, 
    'vagrant_docker_manager_hosts' => manager_host_list.map { |h| h['hostname'] }, 
    'vagrant_docker_worker_hosts' => worker_host_list.map { |h| h['hostname'] }, 
    'local' => ['localhost']}
  
  swarm_host_list = consul_host_list + manager_host_list + worker_host_list

  # Create Hosts
  swarm_host_list.each do |host| 
    hostname = host['hostname']
    vagrant.vm.define hostname do |config|
      config.vm.hostname = hostname
      config.vm.network 'private_network', ip: host['host_ip']
      host['ports'].each do |port|
        config.vm.network 'forwarded_port', guest: port[0], host: port[1]
      end 
      config.vm.provision :hosts
      config.vm.provision :ansible do |ansible|
        ansible.playbook = host['ansible_playbook']
        ansible.groups   = ansible_groups
        ansible.raw_arguments = ['--timeout=30', "-e consul_ip=#{consul_host_ip} consul_port=8500"]
        ansible.limit = host['ansible_group'],
        ansible.host_key_checking = false
      end
    end
  end
end