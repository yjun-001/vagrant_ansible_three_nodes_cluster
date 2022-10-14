# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2004"
  #config.vm.hostname = "hpc-node"

  ssh_prv_key = File.read("#{Dir.home}/.ssh/id_rsa")
  ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip

  config.vm.define "node1" do |machine|
    machine.vm.hostname = "node1"
    #machine.vm.network "forwarded_port", guest: 22, host: 2201, id: "ssh", auto_correct: false
    machine.vm.network "private_network", ip: "192.168.56.15"
    machine.vm.provision "shell", inline: <<-SHELL
      if grep -sq "#{ssh_pub_key}" /home/vagrant/.ssh/authorized_keys; then
        echo "SSH keys already provisioned."
        return;
      fi
      echo "SSH key provisioning."
      mkdir -p /home/vagrant/.ssh/
      touch /home/vagrant/.ssh/authorized_keys
      echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
      echo #{ssh_pub_key} > /home/vagrant/.ssh/id_rsa.pub
      chmod 644 /home/vagrant/.ssh/id_rsa.pub
      # echo 'Host 192.168.*.*' >> /home/vagrant/.ssh/config
      # echo 'StrictHostKeyChecking no' >> /home/vagrant/.ssh/config
      # echo 'UserKnownHostsFile /dev/null' >> /home/vagrant/.ssh/config
      # chmod -R 600 /home/vagrant/.ssh/config
    SHELL
  end

  config.vm.define "node2" do |machine|
    machine.vm.hostname = "node2"
    machine.vm.network "private_network", ip: "192.168.56.16"
    machine.vm.provision "shell", inline: <<-SHELL
      if grep -sq "#{ssh_pub_key}" /home/vagrant/.ssh/authorized_keys; then
        echo "SSH keys already provisioned."
        return;
      fi
      echo "SSH key provisioning."
      mkdir -p /home/vagrant/.ssh/
      touch /home/vagrant/.ssh/authorized_keys
      echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
      echo #{ssh_pub_key} > /home/vagrant/.ssh/id_rsa.pub
      chmod 644 /home/vagrant/.ssh/id_rsa.pub
    SHELL
  end

  config.vm.define "master" do |machine|
    machine.vm.hostname = "master"
    machine.vm.network "private_network", ip: "192.168.56.4"

    # insert vagrant ssh insecure key to master host
    machine.vm.provision "shell", inline: <<-SHELL
      if grep -sq "#{ssh_pub_key}" /home/vagrant/.ssh/authorized_keys; then
        echo "SSH keys already provisioned."
        return;
      fi
      echo "SSH key provisioning."
      mkdir -p /home/vagrant/.ssh/
      touch /home/vagrant/.ssh/authorized_keys
      echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
      echo #{ssh_pub_key} > /home/vagrant/.ssh/id_rsa.pub
      chmod 644 /home/vagrant/.ssh/id_rsa.pub
      echo "#{ssh_prv_key}" > /home/vagrant/.ssh/id_rsa
      chmod 600 /home/vagrant/.ssh/id_rsa
      chown -R vagrant:vagrant /home/vagrant
    SHELL

    # fix ubuntu ERROR: '~ansible' by install manually
    machine.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y ansible 
    SHELL

    machine.vm.synced_folder 'ansible', '/vagrant', id: "vagrant-root", disabled: false, mount_options: ["dmode=775"]
    #machine.vm.provision "file", source: "./ansible.cfg", destination: "/home/vagrant/.ansible.cfg"
    machine.vm.provision "ansible_local" do |ansible|
      ansible.become   = true
      ansible.limit    = "all" # or only "nodes" group, etc.
      ansible.playbook = "provision.yaml"
      ansible.inventory_path = "/vagrant/inventory"
      ansible.verbose = true
      ansible.install = true
      #vagrant_synced_folder_default_type = ""
    end
  end
end
