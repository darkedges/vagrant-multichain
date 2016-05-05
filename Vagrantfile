# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"
VAGRANT_VM_PROVIDER = "virtualbox"
ANSIBLE_RAW_SSH_ARGS = []


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  num_server_nodes = (ENV['NUM_NODES'] || 2).to_i


  (1..num_server_nodes-1).each do |n|
    ANSIBLE_RAW_SSH_ARGS << "-o IdentityFile=#{ENV["VAGRANT_DOTFILE_PATH"]}/machines/machine#{n}/#{VAGRANT_VM_PROVIDER}/private_key"
  end


  num_server_nodes.times do |n|
    node_index = n+1
    config.vm.define "multichain#{node_index}" , autostart: true, primary: true do |multichain|
      multichain.vm.box = "bento/centos-6.7"
      multichain.vm.network :private_network, ip: "192.168.50.#{ 200 + node_index }"
      multichain.hostmanager.aliases = ["multichain#{node_index}.vm.versent.local"]

      #VirtualBox settings
      multichain.vm.provider "virtualbox" do |vb|
        vb.customize ["modifyvm", :id, "--memory", "756"]
        vb.customize ["modifyvm", :id, "--name", "multichain#{node_index}"]
        vb.customize ["modifyvm", :id, "--cpus", "2"]
        vb.customize ["modifyvm", :id, "--ioapic", "on"]
        vb.customize ["modifyvm", :id, "--cpuexecutioncap", "75"]
        vb.customize ["modifyvm", :id, "--paravirtprovider", "kvm"]
      end 
      multichain.vbguest.auto_update = true

      if node_index == num_server_nodes
        multichain.vm.provision :ansible do |ansible|
          ansible.playbook = "provision-multichain.yml"
          ansible.limit = 'all'
          ansible.inventory_path = "static_inventory"
          ansible.raw_ssh_args = ANSIBLE_RAW_SSH_ARGS
        end
      end
    end
  end
end

