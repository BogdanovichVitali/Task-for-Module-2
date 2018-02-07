vm_count = Integer(ENV["VM_COUNT"]) || 1

workers_config = "worker.list=lb \n" + 
  "worker.lb.type=lb \n" + 
  "worker.lb.balance_workers="

worker_names = []
workers = ""
(1..vm_count).each do |counter|
  worker_names.push("tomcat#{counter}")
  workers += "worker.tomcat#{counter}.host=192.168.1.#{counter + 10} \n" + 
    "worker.tomcat#{counter}.port=8009 \n" + 
    "worker.tomcat#{counter}.type=ajp13 \n"
end
workers_config += worker_names.join(", ") + "\n" + workers
  
Vagrant.configure("2") do |config|
  config.vm.box = "bertvv/centos72" 
  
	# Apache
  config.vm.define "apache" do |apache|
    apache.vm.hostname = "apacher"
    apache.vm.network "forwarded_port", guest: 80, host: 18080
    apache.vm.network "private_network", ip: "192.168.1.10"
    config.vm.provision "shell", inline: <<-SHELL
      systemctl stop firewalld
      systemctl disable firewalld
      yum -y install httpd mod_ssl
      cp /vagrant/mod_jk.so /etc/httpd/modules
      echo -e #{workers_config} > /etc/httpd/conf/workers.properties
      echo -e "LoadModule jk_module modules/mod_jk.so \nJkWorkersFile conf/workers.properties \nJkShmFile /tmp/shm \nJkLogFile logs/mod_jk.log \nJkLogLevel info \nJkMount /test* lb" >> /etc/httpd/conf/httpd.conf
      systemctl enable httpd
      systemctl start httpd
      systemctl status httpd
    SHELL
  end

  
  (1..vm_count).each do |counter|
    config.vm.define "tomcat#{counter}" do |tomcat|
      tomcat.vm.hostname = "tomcat1"
      tomcat.vm.network "forwarded_port", guest: 8080, host: 18080 + counter
      tomcat.vm.network "private_network", ip: "192.168.1.#{counter + 10}"
      config.vm.provision "shell", inline: <<-SHELL
        systemctl stop firewalld
        systemctl disable firewalld
        yum install tomcat tomcat-webapps tomcat-admin-webapps -y
        mkdir /usr/share/tomcat/webapps/test
        echo "tomcat#{counter}" > /usr/share/tomcat/webapps/test/index.html
        systemctl enable tomcat
        systemctl start tomcat
        systemctl status tomcat
      SHELL
    end
  end
end
