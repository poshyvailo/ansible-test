# -*- mode: ruby -*-
# vi: set ft=ruby :

hosts = [
  { name: 'ansible', addr: '192.168.33.10' },
  { name: 'node-1', addr: '192.168.33.11' },
  { name: 'node-2', addr: '192.168.33.12' },
  { name: 'node-3', addr: '192.168.33.11' },
]

Vagrant.configure("2") do |config|
  hosts.each do |host|
    config.vm.define host[:name] do |node|
      node.vm.box = "ubuntu/xenial64"
      node.vm.hostname = host[:name]
  
      node.vm.network "private_network", ip: host[:addr]
  
      node.vm.provider "virtualbox" do |provider|
        provider.name = host[:name]
        provider.cpus = 1
        provider.memory = 1024
      end
      
      node.vm.provision "file", source: "id_rsa", destination: "/home/vagrant/.ssh/id_rsa"
      public_key = File.read("id_rsa.pub")
      node.vm.provision :shell, :inline =>"
          echo 'Copying ansible-vm public SSH Keys to the VM'
          mkdir -p /home/vagrant/.ssh
          chmod 700 /home/vagrant/.ssh
          echo '#{public_key}' >> /home/vagrant/.ssh/authorized_keys
          chmod -R 600 /home/vagrant/.ssh/authorized_keys
          echo 'Host 192.168.*.*' >> /home/vagrant/.ssh/config
          echo 'StrictHostKeyChecking no' >> /home/vagrant/.ssh/config
          echo 'UserKnownHostsFile /dev/null' >> /home/vagrant/.ssh/config
          chmod -R 600 /home/vagrant/.ssh/config
          ", privileged: false
      
      
      provision = host[:name] == 'ansible' ? "ansible-provision.sh" : "node-provision.sh"
      node.vm.provision "shell", path: provision
    end
  end
end
