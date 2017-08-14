# -*- mode: ruby -*-
# vi: set ft=ruby :

#Quantity Tomcat VM
TVM = 2

Vagrant.configure("2") do |config|
  config.vm.box = "bertvv/centos72"
  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
  end

  tomcatHash = Hash.new

  (1..TVM).each do |i|
    tomcatHash ["172.20.20.1#{i}"]="tomcat#{i}"
  end

  tomcatHash.each do |ip, host|
    config.vm.define host do |tomcat|
      tomcat.vm.hostname = host
      tomcat.vm.network "private_network", ip: ip
      tomcat.vm.provision "JRE", type: "shell",
        inline: "yum install java-1.8.0-openjdk -y"
      tomcat.vm.provision "tomcat", type: "shell",
        inline: "yum -y install tomcat tomcat-webapps tomcat-admin-webapps"
      tomcat.vm.provision "enable_tomcat", type: "shell",
        inline: "systemctl enable tomcat"
      tomcat.vm.provision "start_tomcat", type: "shell",
        inline: "systemctl start tomcat"
      tomcat.vm.provision "firewalld_tomcat", type: "shell",
        inline: "systemctl stop firewalld"
      tomcat.vm.provision "mkdir", type: "shell",
        inline: "mkdir -p /usr/share/tomcat/webapps/test/"
      tomcat.vm.provision "index.html", type: "shell",
        inline: "echo '#{host}' >> /usr/share/tomcat/webapps/test/index.html"
    end
  end

    config.vm.define "httpd" do |httpd|
      httpd.vm.hostname = "httpd"
      httpd.vm.network "private_network", ip: "172.20.20.10"
      httpd.vm.network "forwarded_port", guest: 80, host: 8080
      httpd.vm.provision "httpd_shell", type: "shell",
        path: "./httpd.sh"
    end

end
