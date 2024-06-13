# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrant.configure("2") do |config|

#   # box settings
#   config.vm.box = "ubuntu/trusty64"

  
#   # network settings
#   #   config.vm.network "forwarded_port", guest: 80, host: 8080
#   # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
#   config.vm.network "private_network", ip: "192.168.33.10"
#   # config.vm.network "public_network"
  
#   # folder settings
#   config.vm.synced_folder ".", "/var/www/html", :mount_options => ["dmode=777", "fmode=666"]
  
#   # provider settings
#   config.vm.provider "virtualbox" do |vb|
# 	vb.memory = "2048"
# 	vb.cpus = 2
#   end

#   # provision settings (commands to run on the VM)
#   config.vm.provision "shell", inline: <<-SHELL
#     apt-get update
#     apt-get install -y apache2
#   SHELL
# end


Vagrant.configure("2") do |config|
	config.vm.box = "debian/bullseye64"
	# config.vm.network "private_network", ip: "192.168.33.10"
	config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
	config.vm.provision "shell", inline: <<-SHELL
	  apt-get update -y
	  apt-get upgrade -y
  
	  apt-get install -y nginx
	  systemctl start nginx
	  systemctl enable nginx

	  debconf-set-selections <<< 'mysql-server mysql-server/root_password password root'
	  debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password root'
	  apt-get install -y mysql-server
	  systemctl start mysql
	  systemctl enable mysql
  
	  apt-get install -y php-fpm php-mysql
	  systemctl start php7.4-fpm
	  systemctl enable php7.4-fpm

	  apt-get install -y curl
  
	  cat <<EOF > /etc/nginx/sites-available/default
	  server {
		  listen 80;
		  server_name localhost;
		  root /var/www/html;
		  index index.php index.html index.htm;
  
		  location / {
			  try_files \$uri \$uri/ =404;
		  }
  
		  location ~ \\.php$ {
			  include snippets/fastcgi-php.conf;
			  fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
		  }
  
		  location ~ /\\.ht {
			  deny all;
		  }
	  }
	  EOF
  
	  systemctl restart nginx
	SHELL
  end
  