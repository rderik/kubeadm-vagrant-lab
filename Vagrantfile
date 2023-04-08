# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
#
# Reference: https://github.com/kodekloudhub/certified-kubernetes-administrator-course/blob/master/Vagrantfile
BOX_IMAGE = "bento/ubuntu-20.04"
MASTER_NODE_COUNT = 1
WORKER_NODE_COUNT = 2
IP_NW = "192.168.56."

def set_hosts(config)
  (1..MASTER_NODE_COUNT).each do |i|
    config.vm.provision "shell", inline: "echo '#{IP_NW}#{10+i} master#{i}' >> /etc/hosts"
    config.vm.provision "shell", inline: "echo '#{IP_NW}#{10+i} master#{i}' >> /home/vagrant/hosts"
  end
  (1..WORKER_NODE_COUNT).each do |i|
    config.vm.provision "shell", inline: "echo '#{IP_NW}#{20+i} node#{i}' >> /etc/hosts"
    config.vm.provision "shell", inline: "echo '#{IP_NW}#{20+i} node#{i}' >> /home/vagrant/hosts"
  end
end

Vagrant.configure("2") do |config|
  (1..MASTER_NODE_COUNT).each do |i|
    config.vm.define "master#{i}" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "master#{i}"
      subconfig.vm.network :private_network, ip: "#{IP_NW}#{10+i}"
      subconfig.vm.network "forwarded_port", guest: 22, host: "#{2230 + i}"
      subconfig.vm.provider "parallels" do |vb|
        vb.name = "master#{i}"
        vb.update_guest_tools = true
        vb.linked_clone = true
        if i == 1
          vb.memory = 2048 
        else
          vb.memory = 1024
        end
        vb.cpus = 2
      end
      set_hosts(subconfig)
    end
  end
  
  (1..WORKER_NODE_COUNT).each do |i|
    config.vm.define "node#{i}" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "node#{i}"
      subconfig.vm.network :private_network, ip: "#{IP_NW}#{20+i}"
      subconfig.vm.network "forwarded_port", guest: 22, host: "#{2240 + i}"
      subconfig.vm.provider "parallels" do |vb|
        vb.name = "node#{i}"
        vb.update_guest_tools = true
        vb.linked_clone = true
        vb.memory = 512
        vb.cpus = 1
      end
      set_hosts(subconfig)
    end
  end
end
