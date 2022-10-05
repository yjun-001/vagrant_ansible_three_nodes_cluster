# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2004"
  #config.vm.hostname = "hpc-node"

  config.vm.define "node1" do |machine|
    machine.vm.hostname = "node1"
    machine.vm.network "private_network", ip: "192.168.56.15"
    #virtualbox__intnet: true, name: "hpc_network"
  end

  config.vm.define "node2" do |machine|
    machine.vm.hostname = "node2"
    machine.vm.network "private_network", ip: "192.168.56.16"
    #virtualbox__intnet: "hpc_network"
  end

  config.vm.define "master" do |machine|
    machine.vm.hostname = "master"
    machine.vm.network "private_network", ip: "192.168.56.4"
    #virtualbox__intnet: "hpc_network"

    machine.vm.synced_folder '.', '/vagrant'
    # fix ubuntu ERROR: '~ansible' by install manually
    machine.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y ansible 
    SHELL

    machine.vm.provision "ansible_local" do |ansible|
      ansible.playbook       = "provision.yaml"
      ansible.limit          = "all" # or only "nodes" group, etc.
      ansible.inventory_path = "inventory"
    end
  end
end
