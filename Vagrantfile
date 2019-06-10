# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.ssh.username = 'centos'
  config.vm.allowed_synced_folder_types = [:rsync]
  #config.ssh.private_key_path = "~/.ssh/id_rsa.pub"
  #config.vm.synced_folder '.', '/vagrant', disabled: true, type: 'rsync'

  config.vm.provider :openstack do |os|
    os.openstack_auth_url               = 'http://ruffner.rc.uab.edu:5000/v3'
    os.identity_api_version             = '3'
    os.image                            = 'CentOS-7-x86_64-GenericCloud-1905'
    os.floating_ip_pool                 = 'bright-external-flat-externalnet'

    os.flavor                           = 'm1.medium'
    os.project_name                     = "******"
    os.user_domain_name                 = "default"
    os.project_domain_name              = "default"
    os.username                         = "*****"
    os.tenant_name                      = "*****"
    os.password                         = "*****"
    os.region                           = "openstack"
    os.networks                         = ['testnet','clusternet']
    #os.keypair_name                     = "MacBook"
    os.security_groups                  = ["default"]
  end

  config.vm.define "ohpc" do |ohpc|

    ohpc.ssh.host                      = '164.111.161.145'
    ohpc.vm.hostname                    = "ohpc"
    ohpc.vm.provider :openstack do |ohpc|

      ohpc.server_name                   = 'ohpc'
      ohpc.floating_ip                   = "192.168.16.145"
    end
  end

  config.vm.define "ood" do |ood|
    ood.vm.box = "centos/7"
    # version placeholder for selecting specific vagrant boxes
    # used mainly for debugging and sanity checking
    # leave commented to use the latest version in the local cache
    #ood.vm.box_version = "1804.02"
    ood.vm.hostname = "ood"
    ood.vm.network "private_network", ip: "10.1.1.254", virtualbox__intnet: "compute"
    ood.vm.network "forwarded_port", guest: 80, host: 8080,
      auto_correct: true
    ood.vm.network "forwarded_port", guest: 443, host: 8443,
      auto_correct: true
  end



  # define user's key and insecure default
  # insecure default is required for initial provisioning
  #config.ssh.private_key_path = ["~/.ssh/id_rsa", "~/.vagrant.d/insecure_private_key"]
  # append user's key to vagrant config to avoid overwrite of existing authorized_keys
  # https://stackoverflow.com/a/31153912/8928529
  config.vm.provision "ssh_pub_key", type: "shell" do |s|
    ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
    s.inline = <<-SHELL
      echo #{ssh_pub_key} >> /home/centos/.ssh/authorized_keys
    SHELL
  end

  config.vm.provision "shell", inline: <<-SHELL
    if [ -f /vagrant/localenv.sh ]; then
      . /vagrant/localenv.sh
    fi
    hostnamectl set-hostname ohpc
    yum install -y epel-release
    yum install -y ansible git vim bash-completion
    yum install -y NetworkManager
    systemctl restart NetworkManager
    nmcli con mod "Wired connection 1" connection.id "eth1"
    ansible-playbook -c local -i /vagrant/CRI_XCBC/hosts -l `hostname` /vagrant/CRI_XCBC/site.yaml -b

  SHELL

end
