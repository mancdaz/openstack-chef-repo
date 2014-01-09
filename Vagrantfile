Vagrant.require_version ">= 1.1"

#Vagrant.require_plugin "vagrant-berkshelf"
#Vagrant.require_plugin "vagrant-chef-zero"
Vagrant.require_plugin "vagrant-omnibus"

Vagrant.configure("2") do |config|
  # Berkshelf plugin configuration
  config.berkshelf.enabled = false

  # Chef-Zero plugin configuration
  config.chef_zero.enabled = false
  config.chef_zero.chef_repo_path = "."

  # Omnibus plugin configuration
  config.omnibus.chef_version = :latest

  # Port forwarding rules, for access to openstack services
  config.vm.network "forwarded_port", guest: 443, host: 8443     # dashboard-ssl
  config.vm.network "forwarded_port", guest: 4000, host: 4000    # chef-zero
  config.vm.network "forwarded_port", guest: 8773, host: 8773    # compute-ec2-api
  config.vm.network "forwarded_port", guest: 8774, host: 8774    # compute-api

  # OpenStack-related settings
  config.vm.network "private_network", ip: "33.33.33.60"
  config.vm.network "private_network", ip: "192.168.100.60"
  chef_environment = "vagrant"
  chef_run_list = [ "role[allinone-compute]" ]

  # virtualbox provider settings
  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--cpus", 2]
    vb.customize ["modifyvm", :id, "--memory", 2048]
    vb.customize ["modifyvm", :id, "--nicpromisc2", "allow-all"]
    vb.customize ["modifyvm", :id, "--nicpromisc3", "allow-all"]
  end

  # rackspace provider settings (personal settings like api_key are in ~/.vagrant.d/Vagrantfile)
  config.vm.provider :rackspace do |rs|
    rs.disk_config     = "MANUAL"
  end

# Ubuntu 12.04 ON RACKSPACE CLOUD config
  config.vm.define :rsubun  do |rsubun|
    rsubun.vm.hostname = "rsubun"
    rsubun.vm.box = "dummy"
    rsubun.vm.box_url = "https://github.com/mitchellh/vagrant-rackspace/raw/master/dummy.box"
    rsubun.vm.provision :chef_client do |chef|
      chef.environment            = chef_environment
      chef.run_list               = chef_run_list.unshift("recipe[apt::cacher-client]")
      chef.chef_server_url        = "https://162.13.94.31:443/"
      chef.validation_client_name = "chef-validator"
      chef.validation_key_path    = "~/.chef/chef-validator.pem"
      chef.delete_node            = true
      chef.delete_client          = true
    end
    rsubun.vm.provider :rackspace do |rs|
      rs.flavor        = /8GB/
      rs.image         = /Precise Pangolin/
    end
  end

# CentOS 6.4 ON RACKSPACE CLOUD config
  config.vm.define :rscent  do |rscent|
    rscent.ssh.pty = true
    rscent.vm.hostname = "rscent"
    rscent.vm.box = "dummy"
    rscent.vm.box_url = "https://github.com/mitchellh/vagrant-rackspace/raw/master/dummy.box"
    rscent.vm.provision :chef_client do |chef|
      chef.environment            = chef_environment
      chef.chef_server_url        = "https://162.13.94.31:443/"
      chef.validation_client_name = "chef-validator"
      chef.validation_key_path    = "~/.chef/chef-validator.pem"
      chef.delete_node            = true
      chef.delete_client          = true
    end
    rscent.vm.provider :rackspace do |rs|
      rs.flavor            = /8 GB Performance/
      rs.image             = /CentOS 6\.4/
    end
  end

  # Ubuntu 12.04 Config
  config.vm.define :ubuntu1204 do |ubuntu1204|
    ubuntu1204.vm.hostname = "ubuntu1204"
    ubuntu1204.vm.box = "opscode-ubuntu-12.04"
    ubuntu1204.vm.box_url = "https://opscode-vm-bento.s3.amazonaws.com/vagrant/opscode_ubuntu-12.04_provisionerless.box"
    ubuntu1204.vm.provision :chef_client do |chef|
      chef.environment = chef_environment
      chef.run_list = chef_run_list.unshift("recipe[apt::cacher-client]")
    end
  end

# Centos 6.4 Config
  config.vm.define :centos64 do |centos64|
    centos64.ssh.pty = true
    centos64.vm.hostname = "centos64"
    centos64.vm.box = "opscode-centos-6.4"
    centos64.vm.box_url = "https://opscode-vm-bento.s3.amazonaws.com/vagrant/opscode_centos-6.4_provisionerless.box"
    centos64.vm.provision :chef_client do |chef|
      chef.environment = chef_environment
      chef.run_list = chef_run_list
    end
  end

end
