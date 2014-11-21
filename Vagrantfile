# -*- mode: ruby -*-
# vi: set ft=ruby :

# Config
enviroment             = "development"
ruby_version_release   = "2.0.0-p247"
folder_shared_guest    = "/home/vagrant"
folder_shared_host     = "."
folder_apps = "#{folder_shared_guest}/apps" # add to .gitigore (don't forget this)
folder_ssh_config = "~/.ssh"


# init
VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # S.O.
  config.vm.box = "ubuntu/trusty64"

  # ssh config
  config.ssh.forward_agent = true
  config.ssh.insert_key    = true
  config.ssh.password      = "vagrant"

  # 2GB of RAM
  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "2048"]
  end

  # shared folder
  config.vm.synced_folder folder_shared_host, folder_shared_guest#, nfs: true

  # network
  config.vm.network :forwarded_port, guest: 5000, host: 5000

  # copy rsa keys from host to guest
  config.vm.provision "file", source: "~/.ssh/id_rsa.pub",  destination: "#{folder_ssh_config}/id_rsa.pub"
  config.vm.provision "file", source: "~/.ssh/id_rsa",      destination: "#{folder_ssh_config}/id_rsa"
  config.vm.provision "file", source: "~/.ssh/known_hosts", destination: "#{folder_ssh_config}/known_hosts"

  # chef
  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = ["cookbooks"]

    chef.add_recipe "apt"
    chef.add_recipe "git"
    chef.add_recipe "ruby_build"
    chef.add_recipe "rvm"
    chef.add_recipe "rvm::system"
    chef.add_recipe "rvm::vagrant"
    chef.add_recipe "rvm::gem_package"
    chef.add_recipe "vim"
    chef.add_recipe "nodejs"
    chef.add_recipe "postgresql::server"
    chef.add_recipe "postgresql::client"
    chef.add_recipe "mongodb"
    chef.add_recipe "redisio"
    chef.add_recipe "redisio::enable"
    chef.add_recipe "imagemagick"
    chef.add_recipe "install_sc_apps"

    # cookbook configs
    chef.json = {
      sc_config:{
        folder_apps:       folder_apps,
        enviroment:        enviroment,
        folder_ssh_config: folder_ssh_config,
      },
      rvm: {
        rubies: [ruby_version_release],
        default_ruby: ruby_version_release,
        global_gems: [
          { name: 'bundler' },
          { name: 'rake'    },
          # { name: 'rails'   },
          { name: 'chef'    },
        ],
      },      
      postgresql: {
        version: "9.3",
        password: {
          postgres: "12345678"
        }
      }
    }

    chef.log_level = "debug" 
  end


end
