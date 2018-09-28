# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

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
    #ohpc.vm.customize ["modifyvm", :id, "--name", "ohpc"]
  end


  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.memory = "2048"
  end

  config.vm.provision "shell", inline: <<-SHELL
    if [ -f /vagrant/localenv.sh ]; then
      . /vagrant/localenv.sh
    fi

    yum install -y ansible git vim bash-completion
    ansible-playbook -c local -i /vagrant/CRI_XCBC/hosts -l `hostname` /vagrant/CRI_XCBC/site.yaml

  SHELL

end
