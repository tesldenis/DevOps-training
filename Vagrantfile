# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "bertvv/centos72"
  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
  end

  config.vm.define "server1" do |server1|
    server1.vm.hostname = "server1"
    server1.vm.network "private_network", ip: "172.20.20.10"
    server1.vm.provision "host-server2", type: "shell",
      inline: "echo '172.20.20.11 server2' >> /etc/hosts"
  end

  config.vm.define "server2" do |server2|
    server2.vm.hostname = "server2"
    server2.vm.network "private_network", ip: "172.20.20.11"
    server2.vm.provision "host-server1", type: "shell",
      inline: "echo '172.20.20.10 server1' >> /etc/hosts"
    server2.vm.provision "yum", type: "shell",
      inline: "yum install git -y"
    server2.vm.provision "git", type: "shell",
      inline: "git clone https://github.com/tesldenis/DevOps-training.git"
    server2.vm.provision "cat", type: "shell",
      inline: "cat DevOps-training/hello.txt"
  end
end
