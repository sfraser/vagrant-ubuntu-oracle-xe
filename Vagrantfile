# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  config.vm.hostname = "oracle"

  # Forward Oracle port
  config.vm.network :forwarded_port, guest: 1521, host: 1521

  # Provider-specific configuration so you can fine-tune various backing
  # providers for Vagrant. These expose provider-specific options.
  config.vm.provider :virtualbox do |vb|
    # Use VBoxManage to customize the VM
    vb.customize ["modifyvm", :id,
                  "--name", "oracle",
                  # Oracle claims to need 512MB of memory available minimum
                  "--memory", "512",
                  # Enable DNS behind NAT
                  "--natdnshostresolver1", "on"]
  end

  config.vm.provision :shell, :inline => "echo \"America/New_York\" | sudo tee /etc/timezone && dpkg-reconfigure --frontend noninteractive tzdata"

  config.vbguest.auto_update = false

  config.vm.provision :puppet do |puppet|
    puppet.manifests_path = "manifests"
    puppet.module_path = "modules"
    puppet.manifest_file = "base.pp"
    puppet.options = "--verbose --trace"
  end

  # A little confusing but we are going to use Chef to install Java 7... that's what I have working for now: -SEF 2/2014
  # Plugins
  config.berkshelf.enabled = true
  config.omnibus.chef_version = :latest

  # Network
  config.vm.network :forwarded_port, guest: 80, host: 8080

  # Chef solo provisioning
  config.vm.provision :chef_solo do |chef|
    
    chef.json = {
      "java" => {
        "install_flavor" => "oracle",
        "jdk_version" => "7",
        "oracle" => {
          "accept_oracle_download_terms" => true
        }
      }
    }
    
  chef.add_recipe "java"

end
