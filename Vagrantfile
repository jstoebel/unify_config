# Defines our Vagrant environment
#
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # create mgmt node
  config.vm.define :mgmt do |mgmt_config|
      # the machine doing the provisioning. (my laptop probobly)
      mgmt_config.vm.box = "bento/ubuntu-16.04"
      mgmt_config.vm.hostname = "mgmt"
      mgmt_config.vm.network :private_network, ip: "10.0.15.10"
      mgmt_config.vm.provider "virtualbox" do |vb|
        vb.memory = "256"
      end
      mgmt_config.vm.provision :shell, path: "bootstrap-mgmt.sh"
  end

  config.vm.define :node do |node|
      # the droplet to provision
      node.vm.box = "bento/ubuntu-16.04"
      node.vm.hostname = "web1"
      node.vm.network :private_network, ip: "10.0.15.11"
      node.vm.network "forwarded_port", guest: 80, host: 8080
      node.vm.provider "virtualbox" do |vb|
        vb.memory = "1024"
      end
  end

end
