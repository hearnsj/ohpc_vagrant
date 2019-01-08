# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # don't configure host-specific keys, config will use the user's key
  config.ssh.insert_key = false

  config.vm.define "ohpc" do |ohpc|
    ohpc.vm.box = "ravi89/centos7.5"
    ohpc.vm.box_version = "1"
    ohpc.vm.hostname = "ohpc"
    ohpc.vm.network "private_network", ip: "10.1.1.1", virtualbox__intnet: "compute"
    #ohpc.vm.customize ["modifyvm", :id, "--name", "ohpc"]
  end

  config.vm.define "ood" do |ood|
    ood.vm.box = "ravi89/centos7.5"
    ood.vm.box_version = "1"
    ood.vm.hostname = "ood"
    ood.vm.network "private_network", ip: "10.1.1.254", virtualbox__intnet: "compute"
    ood.vm.network "forwarded_port", guest: 80, host: 8080,
      auto_correct: true
    ood.vm.network "forwarded_port", guest: 443, host: 8443,
      auto_correct: true
  end

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.memory = "2048"
  end

  # define user's key and insecure default
  # insecure default is required for initial provisioning
  config.ssh.private_key_path = ["~/.ssh/id_rsa", "~/.vagrant.d/insecure_private_key"]
  # append user's key to vagrant config to avoid overwrite of existing authorized_keys
  # https://stackoverflow.com/a/31153912/8928529
  config.vm.provision "ssh_pub_key", type: "shell" do |s|
      ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
      s.inline = <<-SHELL
        echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
      SHELL
  end

  config.vm.provision "shell", inline: <<-SHELL
    if [ -f /vagrant/localenv.sh ]; then
      . /vagrant/localenv.sh
    fi

    yum install -y ansible git vim bash-completion
    ansible-playbook -c local -i /vagrant/CRI_XCBC/hosts -l `hostname` /vagrant/CRI_XCBC/site.yaml -b

  SHELL

end
