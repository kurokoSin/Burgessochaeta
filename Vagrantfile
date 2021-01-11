Vagrant.configure(2) do |config|
  
  # dev-VM
  config.vm.define "dev" do |dev|
    dev.vm.box = "generic/ubuntu1804"
    dev.vm.hostname = "ansible-dev"
    dev.vm.network "private_network", ip: "172.16.20.103"
    dev.vm.network :forwarded_port, guest: 80, host: 8000, id: "http"
    dev.vm.synced_folder './dev/', '/vagrant', type: '9p', :mount_options => ['dmode=775', 'fmode=664']

    dev.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus = 2
    end
    
    dev.vm.provision "shell", inline: <<-SHELL
      # DNS config
      sed -i.bak -e "s/^DNS=.*$/DNS=8.8.8.8 8.8.4.4/g" /etc/systemd/resolved.conf
      systemctl restart systemd-resolved.service
      
      # Package
      apt update
      apt install -y aptitude
    SHELL
    
  end

  # web-VM
  config.vm.define "web" do |web|
    web.vm.box = "generic/ubuntu1804"
    web.vm.hostname = "ansible-web"
    web.vm.network "private_network", ip: "172.16.20.102"
    web.vm.network :forwarded_port, guest: 80, host: 8000, id: "http"
    web.vm.synced_folder './web/', '/vagrant', type: '9p', :mount_options => ['dmode=775', 'fmode=664']

    web.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus = 2
    end
    
    web.vm.provision "shell", inline: <<-SHELL
      # DNS config
      sed -i.bak -e "s/^DNS=.*$/DNS=8.8.8.8 8.8.4.4/g" /etc/systemd/resolved.conf
      systemctl restart systemd-resolved.service
      
      # Package
      apt update
      apt install -y aptitude
    SHELL
    
  end

  # control-VM
  config.vm.define "control" do |control|
    control.vm.box = "generic/ubuntu1804"
    control.vm.hostname = "ansible-control"
    control.vm.network "private_network", ip: "172.16.20.101"
    control.vm.synced_folder './control/', '/vagrant', type: '9p', :mount_options => ['dmode=775', 'fmode=664']

    control.vm.provider "virtualbox" do |vb|
      vb.memory = 512
      vb.cpus = 2
    end

    control.vm.provision "shell", inline: <<-SHELL
      # DNS config
      sed -i.bak -e "s/^DNS=.*$/DNS=8.8.8.8 8.8.4.4/g" /etc/systemd/resolved.conf
      systemctl restart systemd-resolved.service

      apt update 
      apt -y install expect aptitude
      
      # ansible 
      apt -y install software-properties-common
      apt-add-repository --yes --update ppa:ansible/ansible
      apt -y install ansible
      apt -y autoremove
    SHELL

    control.vm.provision "shell", name: "Setup ssh", privileged: false, inline: <<-SHELL
      # ssh
      mkdir -p ~/.ssh
      ssh-keygen -N "" -t ed25519 -f ~/.ssh/id_ed25519
      rm -f ~/.ssh/known_hosts

      # ssh-copy-id
      cp -f /vagrant/expect_sendkey.expect ~/expect_sendkey.expect
      chmod 774 ~/expect_sendkey.expect
      ~/expect_sendkey.expect vagrant@172.16.20.101
      ~/expect_sendkey.expect vagrant@172.16.20.102
      ~/expect_sendkey.expect vagrant@172.16.20.103
    SHELL

    control.vm.provision "shell", name: "Setup Ansible", privileged: false, inline: <<-SHELL
      cp -pR /vagrant/playbook/ ~/playbook/ 
    SHELL
    
    control.vm.provision "shell", name: "Setup Ansible( root )", inline: <<-SHELL
      cp -p /vagrant/ansible_conf.sh /etc/profile.d/ansible_conf.sh
      chmod 0644 /etc/profile.d/ansible_conf.sh
      chown root:root /etc/profile.d/ansible_conf.sh
    SHELL
  end

end
