Vagrant.configure(2) do |config|
  # Config 
  # config.ssh.guest_port = 10022
  # config.ssh.keys_only = yes
  
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

      # # Apt Repository Japanese
      # cd /tmp
      # wget -nv https://www.ubuntulinux.jp/ubuntu-ja-archive-keyring.gpg
      # apt-key add /tmp/ubuntu-ja-archive-keyring.gpg
      # 
      # cd /tmp
      # wget -nv https://www.ubuntulinux.jp/ubuntu-jp-ppa-keyring.gpg
      # apt-key add /tmp/ubuntu-jp-ppa-keyring.gpg
      # 
      # mkdir -p /etc/apt/sources.list.d
      # cd /etc/apt/sources.list.d
      # wget -nv https://www.ubuntulinux.jp/sources.list.d/bionic.list
      # mv bionic.list ubuntu-ja.list
      # 
      # export DEBIAN_FRONTEND=noninteractive
      # apt -y update
      # apt -y install ubuntu-defaults-ja
      
      apt update 
      # expect
      apt -y install expect aptitude
      # apt -y install python-pip
      # pip install pexpect
      
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
