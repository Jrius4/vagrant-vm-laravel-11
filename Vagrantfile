Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2004"
  config.vm.hostname = "lab1vm"

  # Assign a local IP address
  config.vm.network "private_network", ip: "192.168.56.10"

  # Sync the project folder
  config.vm.synced_folder ".", "/var/www/html"

  # Provision the virtual machine with a shell script
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y apache2
    apt-get install -y mysql-server
    apt-get install -y php libapache2-mod-php php-mysql
    apt-get install -y php-cli php-mbstring unzip
    apt-get install -y curl
    apt-get install -y git
    apt-get install -y php-xml php-zip php-curl

    # installs nvm (Node Version Manager)
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

    # download and install Node.js (you may need to restart the terminal)
    nvm install 20

    # verifies the right Node.js version is in the environment
    # node -v # should print `v20.16.0`

    # verifies the right npm version is in the environment
    # npm -v # should print `10.8.1`

    # Setup MySQL root password and create a database
    debconf-set-selections <<< 'mysql-server mysql-server/root_password password root'
    debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password root'
  

    # Allow remote access to MySQL (optional)
    sed -i 's/bind-address\s*=.*$/bind-address = 0.0.0.0/' /etc/mysql/mysql.conf.d/mysqld.cnf
    mysql -uroot -proot -e "CREATE DATABASE vagrant_db"
    mysql -uroot -proot -e "GRANT ALL ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION"
    mysql -uroot -proot -e "FLUSH PRIVILEGES"
    service mysql restart

    # Configure Apache to use /var/www/html as the document root
    # rm -rf /var/www/html
    ln -fs /vagrant /var/www/html

    # Enable mod_rewrite for Apache
    a2enmod rewrite
    service apache2 restart
  SHELL
end
