Vagrant.configure("2") do |config|
  config.vm.box = "bertvv/centos72" 
  
	# Apache
  config.vm.define "apache" do |apache|
    apache.vm.hostname = "apacher"
    apache.vm.network "forwarded_port", guest: 80, host: 18080
    apache.vm.network "private_network", ip: "192.168.1.10"
    config.vm.provision "shell", inline: <<-SHELL
      sudo systemctl stop firewalld
      sudo systemctl disable firewalld
      sudo yum -y install httpd mod_ssl
      sudo systemctl enable httpd
      sudo systemctl start httpd
      sudo cp /vagrant/mod_jk.so /etc/httpd/modules
      sudo echo -e "worker.list=lb \nworker.lb.type=lb \nworker.lb.balance_workers=tomcat1, tomcat2 \nworker.tomcat1.host=192.168.1.11 \nworker.tomcat1.port=8009 \nworker.tomcat1.type=ajp13 \nworker.tomcat2.host=192.168.1.12 \nworker.tomcat2.port=8009 \nworker.tomcat2.type=ajp13" > /etc/httpd/conf/workers.properties
      sudo echo -e "LoadModule jk_module modules/mod_jk.so \nJkWorkersFile conf/workers.properties \nJkShmFile /tmp/shm \nJkLogFile logs/mod_jk.log \nJkLogLevel info \nJkMount /test* lb" >> /etc/httpd/conf/httpd.conf
      sudo systemctl restart httpd
      sudo systemctl status httpd
    SHELL
  end

	# Tomcat1.
  config.vm.define "tomcat1" do |tomcat1|
    tomcat1.vm.hostname = "tomcat1"
    tomcat1.vm.network "forwarded_port", guest: 8080, host: 18081
    tomcat1.vm.network "private_network", ip: "192.168.1.11"
    config.vm.provision "shell", inline: <<-SHELL
      sudo systemctl stop firewalld
      sudo systemctl disable firewalld
      sudo yum install tomcat tomcat-webapps tomcat-admin-webapps -y
      sudo mkdir /usr/share/tomcat/webapps/test
      sudo echo "tomcat1" > /usr/share/tomcat/webapps/test/index.html
      sudo systemctl enable tomcat
      sudo systemctl start tomcat
      sudo systemctl status tomcat
    SHELL

  end

  # Tomcat2.
  config.vm.define "tomcat2" do |tomcat2|
    tomcat2.vm.hostname = "tomcat2"
    tomcat2.vm.network "forwarded_port", guest: 8080, host: 18082
    tomcat2.vm.network "private_network", ip: "192.168.1.12"
      config.vm.provision "shell", inline: <<-SHELL
      sudo systemctl stop firewalld
      sudo systemctl disable firewalld
      sudo yum install tomcat tomcat-webapps tomcat-admin-webapps -y
      sudo mkdir /usr/share/tomcat/webapps/test
      sudo echo "tomcat2" > /usr/share/tomcat/webapps/test/index.html
      sudo systemctl enable tomcat
      sudo systemctl start tomcat
      sudo systemctl status tomcat
    SHELL
  end
end
