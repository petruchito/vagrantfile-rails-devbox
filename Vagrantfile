# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

Vagrant.configure("2") do |config|
  current_dir = File.basename(Dir.getwd)
  config.vagrant.plugins = ["vagrant-persistent-storage","vagrant-vbguest"]
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "bento/ubuntu-18.04"
  config.vm.boot_timeout = 600

  config.persistent_storage.enabled = true
  config.persistent_storage.location = "D:/Temp/#{current_dir}.vdi"
  config.persistent_storage.size = 5000
  config.persistent_storage.mountname = 'projects'
  config.persistent_storage.filesystem = 'ext4'
  config.persistent_storage.mountpoint = '/home/vagrant/projects'
  config.persistent_storage.volgroupname = 'persist'
  

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.network "forwarded_port", guest: 3000, host: 3000 # HTTP
  config.vm.network "forwarded_port", guest: 5432, host: 5432 # PostgreSQL
  config.vm.network "forwarded_port", guest: 3306, host: 3306 # MySQL


  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
  #  config.vm.synced_folder "e:/Documents/Devel/Ruby", "/home/vagrant/ruby"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
   config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
     vb.name = current_dir
     vb.memory = "2048"
     vb.linked_clone = true
     vb.customize [ "modifyvm", :id, "--uart1", "off" ] #slow boot fix VBox v6.1
   end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.

    config.vm.provision "shell", inline: <<-SHELL
     chown vagrant:vagrant /home/vagrant/projects
     apt-get update
     apt-get install -y software-properties-common
     apt-add-repository -y ppa:rael-gc/rvm
     apt-get update
     apt remove cmdtest
     apt-get install -y nodejs npm rvm 
     npm install --global yarn npx
SHELL

    config.vm.provision "shell", name: "rvm", privileged: false, inline: <<-SHELL
     sudo usermod -a -G rvm $USER
     bash -lc "rvm user gemsets"
     bash -lc "rvm install ruby-3.0.0"
     bash -lc "rvm --default use ruby"
     bash -lc "rvm use ruby"
     bash -lc "gem install rails"
SHELL

    config.vm.provision "shell", name: "settings", privileged: false, inline: <<-SHELL
     git config --global user.name "petruchito"
     git config --global user.email "freeglider@gmail.com"
     git config --global color.ui true
     git config --global core.editor vim
     cp /vagrant/keys/id_rsa* ~/.ssh/
     chmod 600 ~/.ssh/id_rsa*
     cp /vagrant/keys/known_hosts ~/.ssh/
     chmod 600 ~/.ssh/known_hosts
     cd ~
     git init .
     git remote add origin git@github.com:petruchito/vimrc.git
     git pull origin main
     git submodule init
     git submodule update
SHELL

    config.vm.provision "shell", name: "nvm", privileged: false, inline: <<-SHELL
     source /vagrant/install_nvm.sh
     source ~/.nvm/nvm.sh
     nvm install --lts
SHELL
end
