Vagrant.configure("2") do |config|
    # Servidor Web 1 - Apache
    config.vm.define "web1" do |web1|
      web1.vm.box = "ubuntu/bionic64"
      web1.vm.network "private_network", ip: "192.168.56.10"
      web1.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      web1.vm.synced_folder "./html", "/var/www/html"
      web1.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y apache2
        echo '<h1>Servidor Web 1</h1>' | sudo tee /var/www/html/index.html
        sudo systemctl restart apache2
      SHELL
    end
  
    # Servidor Web 2 - Apache
    config.vm.define "web2" do |web2|
      web2.vm.box = "ubuntu/bionic64"
      web2.vm.network "private_network", ip: "192.168.56.11"
      web2.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      web2.vm.synced_folder "./html", "/var/www/html"
      web2.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y apache2
        echo '<h1>Servidor Web 2</h1>' | sudo tee /var/www/html/index.html
        sudo systemctl restart apache2
      SHELL
    end
  
    # Servidor Nginx - Load Balancer
    config.vm.define "lb" do |lb|
      lb.vm.box = "ubuntu/bionic64"
      lb.vm.network "private_network", ip: "192.168.56.12"
      lb.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      lb.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y nginx
        sudo tee /etc/nginx/sites-available/load_balancer <<EOF
  server {
      listen 80;
      location / {
          proxy_pass http://backend;
      }
  }
  
  upstream backend {
      server 192.168.56.10;
      server 192.168.56.11;
  }
  EOF
        sudo ln -sf /etc/nginx/sites-available/load_balancer /etc/nginx/sites-enabled/
        sudo systemctl restart nginx
      SHELL
    end
  end
  