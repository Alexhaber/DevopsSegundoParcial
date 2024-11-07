# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/bionic64"

  # Servidor Web 1 - Apache
  config.vm.define "web1" do |web1|
    web1.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    web1.vm.network "private_network", ip: "192.168.33.10"
    web1.vm.hostname = "web1"

    # sincronización de  las carpetas del contenido web
    web1.vm.synced_folder ".", "/var/www/html", type: "rsync"

    # configuracion de apache
    web1.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      echo "<h1>Servidor Web 1</h1>" > /var/www/html/index.html
      systemctl restart apache2
    SHELL
  end

  # Servidor Web 2
  # lo mismo que el anterior
  config.vm.define "web2" do |web2|
    web2.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    web2.vm.network "private_network", ip: "192.168.33.11"
    web2.vm.hostname = "web2"

    web2.vm.synced_folder ".", "/var/www/html", type: "rsync"

    web2.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      echo "<h1>Servidor Web 2</h1>" > /var/www/html/index.html
      systemctl restart apache2
    SHELL
  end

  # servidor Nginx
  config.vm.define "loadbalancer" do |lb|
    lb.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    lb.vm.network "private_network", ip: "192.168.33.12"
    lb.vm.hostname = "loadbalancer"

    # intalacion y configuraciones necesarias --->|
    lb.vm.provision "shell", inline: <<-SHELL   
      apt-get update
      apt-get install -y nginx
      cat <<EOL > /etc/nginx/sites-available/default
      upstream backend {
          server 192.168.33.10;
          server 192.168.33.11;
      }

      server {
          listen 80;

          location / {
              proxy_pass http://backend;
          }
      }
      EOL
      systemctl restart nginx
    SHELL
  end
end
