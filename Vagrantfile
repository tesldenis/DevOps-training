# -*- mode: ruby -*-
# vi: set ft=ruby :

#Quantity Tomcat VM
TVM = 3

Vagrant.configure("2") do |config|
  config.vm.box = "bertvv/centos72"
  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
  end
  config.vm.provision "firewalld", type: "shell",
    inline: "systemctl stop firewalld"

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
      httpd.vm.provision "httpd", type: "shell",
        inline: "yum -y install httpd"
      httpd.vm.provision "enable_httpd", type: "shell",
        inline: "systemctl enable httpd"
      httpd.vm.provision "copy", type: "shell",
        inline: "cp /vagrant/mod_jk.so /etc/httpd/modules/"
      httpd.vm.provision "properties_00", type: "shell",
        inline: "echo 'worker.list=lb' >> /etc/httpd/conf/workers.properties"
      httpd.vm.provision "properties_01", type: "shell",
        inline: "echo 'worker.lb.type=lb' >> /etc/httpd/conf/workers.properties"

#Filling Apache configuration depending on the constant TVM
  propertiesHash = Hash.new

  (1..TVM).each do |i|
    propertiesHash ["172.20.20.1#{i}"]="tomcat#{i}"
  end

  tomcats = ""
  isFirst = true
  propertiesHash.each_value {|value|
    if isFirst
       tomcats += value
       isFirst = false
    else
       tomcats += ",#{value}"
    end
  }

  propertiesHash.each do |ip, tomcat|
      httpd.vm.provision "properties_02", type: "shell",
       inline: "echo 'worker.lb.balance_workers=#{tomcats}' >> /etc/httpd/conf/workers.properties"
      httpd.vm.provision "shell",
        inline: "echo 'worker.#{tomcat}.host=#{ip}' >> /etc/httpd/conf/workers.properties"
      httpd.vm.provision "shell",
        inline: "echo 'worker.#{tomcat}.port=8009' >> /etc/httpd/conf/workers.properties"
      httpd.vm.provision "shell",
        inline: "echo 'worker.#{tomcat}.type=ajp13' >> /etc/httpd/conf/workers.properties"
  end

      httpd.vm.provision "conf_00", type: "shell",
        inline: "echo 'LoadModule jk_module modules/mod_jk.so' >> /etc/httpd/conf/httpd.conf"
      httpd.vm.provision "conf_01", type: "shell",
        inline: "echo 'JkWorkersFile conf/workers.properties' >> /etc/httpd/conf/httpd.conf"
      httpd.vm.provision "conf_02", type: "shell",
        inline: "echo 'JkShmFile /tmp/shm' >> /etc/httpd/conf/httpd.conf"
      httpd.vm.provision "conf_03", type: "shell",
        inline: "echo 'JkLogFile logs/mod_jk.log' >> /etc/httpd/conf/httpd.conf"
      httpd.vm.provision "conf_04", type: "shell",
        inline: "echo 'JkLogLevel info' >> /etc/httpd/conf/httpd.conf"
      httpd.vm.provision "conf_05", type: "shell",
        inline: "echo 'JkMount /test* lb' >> /etc/httpd/conf/httpd.conf"
      httpd.vm.provision "start_httpd", type: "shell",
        inline: "systemctl start httpd"
      httpd.vm.provision "restart_httpd", type: "shell",
        inline: "systemctl restart httpd"
  end
end
