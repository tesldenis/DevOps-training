# -*- mode: ruby -*-
# vi: set ft=ruby :

#Quantity VM
QVM = 3

Vagrant.configure("2") do |config|
  config.vm.box = "bertvv/centos72"
  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
  end

  ipdomainHash = Hash.new

  (1..QVM).each do |i|
    ipdomainHash ["172.20.20.1#{i}"]="server#{i}"
  end

  ipdomainHash.each do |ip, host|
    config.vm.define host do |server|
      server.vm.hostname = host
      server.vm.network "private_network", ip: ip
      currentIp = ip
      ipdomainHash.each do |ip, host|
        unless currentIp == ip
        server.vm.provision host, type: "shell",
          inline: "echo '#{ip} #{host}' >> /etc/hosts"
        end
      end

      if host == "server1"
        server.vm.provision "yum", type: "shell",
          inline: "yum install git -y"
        server.vm.provision "git", type: "shell",
          inline: "git clone https://github.com/tesldenis/DevOps-training.git"
        server.vm.provision "cat", type: "shell",
          inline: "cat DevOps-training/hello.txt"
      end
    end
  end
end
