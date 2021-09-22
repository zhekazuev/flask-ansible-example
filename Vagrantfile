# -*- mode: ruby -*-
# vi: set ft=ruby :

# Every Vagrant development environment requires a box. You can search for
# boxes at https://atlas.hashicorp.com/search.
BOX_IMAGE = "zhekazuev/ubuntu-20.10"
BOX_VERSION = "1.0.0"

Vagrant.configure("2") do |config|
  config.vm.provider "virtualbox" do |v|
    v.memory = 512
    v.cpus = 1
  end

  config.vm.define "master" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.box_version = BOX_VERSION
    subconfig.vm.hostname = "master.localhost"
    subconfig.vm.network "private_network", ip: "192.168.99.10"
    subconfig.vm.provision "file", source: "./.hosts", destination: "~/flask-ansible-example/.hosts"
    subconfig.vm.provision "file", source: "./.nginx", destination: "~/flask-ansible-example/.nginx"
    subconfig.vm.provision "file", source: "./.service", destination: "~/flask-ansible-example/.service"
    subconfig.vm.provision "file", source: "./ansible.cfg", destination: "~/flask-ansible-example/ansible.cfg"
    subconfig.vm.provision "file", source: "./deploy.yml", destination: "~/flask-ansible-example/deploy.yml"
    subconfig.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
   
    # Install utils on all machines  
    subconfig.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt install software-properties-common
      sudo add-apt-repository --yes --update ppa:ansible/ansible
      sudo apt install ansible --yes
      ansible --version
      sudo useradd -p $(openssl passwd -crypt ansible) ansible
      sudo apt install sshpass --yes
    SHELL
  end

  config.vm.define "node1" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.box_version = BOX_VERSION
    subconfig.vm.hostname = "node1.localhost"
    subconfig.vm.network "private_network", ip: "192.168.99.11"
    #subconfig.vm.network 'forwarded_port', guest: 80, host: 35001, host_ip: '192.168.99.11'
    subconfig.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"

    subconfig.vm.provision "shell", inline: <<-SHELL
      sudo useradd -m -p $(openssl passwd -crypt ansible) ansible
      sudo apt install sshpass --yes
    SHELL
  end
end
