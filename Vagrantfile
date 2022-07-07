# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV['VAGRANT_NO_PARALLEL'] = 'yes'

VAGRANT_BOX         = "generic/ubuntu2204"
VAGRANT_BOX_VERSION = "4.0.2"
CPUS_MASTER_NODE    = 2
CPUS_WORKER_NODE    = 1
MEMORY_MASTER_NODE  = 2048
MEMORY_WORKER_NODE  = 1024
WORKER_NODES_COUNT  = 1


Vagrant.configure(2) do |config|

  config.vm.provision "shell", path: "bootstrap.sh"

  # Kubernetes Master Server
  config.vm.define "kmaster" do |node|

    node.ssh.insert_key = false
    node.ssh.private_key_path = ["~/.ssh/id_rsa", "~/.vagrant.d/insecure_private_key"]
    node.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/authorized_keys"
    node.ssh.username = "vagrant"
    node.ssh.password = "vagrant"

    node.vm.synced_folder ".", "/vagrant", disabled: true
  
    node.vm.box               = VAGRANT_BOX
    node.vm.box_check_update  = false
    node.vm.box_version       = VAGRANT_BOX_VERSION
    node.vm.hostname          = "kmaster.example.com"

    node.vm.network "private_network", ip: '192.168.56.100', :name => 'VirtualBox Host-Only Ethernet Adapter', :adapter => 2
  
    node.vm.provider :virtualbox do |v|
      v.name    = "kmaster"
      v.memory  = MEMORY_MASTER_NODE
      v.cpus    = CPUS_MASTER_NODE
    end
  
    node.vm.provider :libvirt do |v|
      v.memory  = MEMORY_MASTER_NODE
      v.nested  = true
      v.cpus    = CPUS_MASTER_NODE
    end
  
    node.vm.provision "shell", path: "bootstrap_kmaster.sh"
  
  end


  # Kubernetes Worker Nodes
  (1..WORKER_NODES_COUNT).each do |i|

    config.vm.define "kworker#{i}" do |node|

      node.ssh.insert_key = false
      node.ssh.private_key_path = ["~/.ssh/id_rsa", "~/.vagrant.d/insecure_private_key"]
      node.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/authorized_keys"
      node.ssh.username = "vagrant"
      node.ssh.password = "vagrant"

      node.vm.synced_folder ".", "/vagrant", disabled: true

      node.vm.box               = VAGRANT_BOX
      node.vm.box_check_update  = false
      node.vm.box_version       = VAGRANT_BOX_VERSION
      node.vm.hostname          = "kworker#{i}.example.com"

      node.vm.network "private_network", ip: "192.168.56.10#{i}", :name => 'VirtualBox Host-Only Ethernet Adapter', :adapter => 2

      node.vm.provider :virtualbox do |v|
        v.name    = "kworker#{i}"
        v.memory  = MEMORY_WORKER_NODE
        v.cpus    = CPUS_WORKER_NODE
      end

      node.vm.provider :libvirt do |v|
        v.memory  = MEMORY_WORKER_NODE
        v.nested  = true
        v.cpus    = CPUS_WORKER_NODE
      end

      node.vm.provision "shell", path: "bootstrap_kworker.sh"

    end

  end

end
