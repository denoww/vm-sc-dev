# -*- mode: ruby -*-
# vi: set ft=ruby :

# Config
enviroment             = "development"
ruby_version_release   = "2.0.0-p247"
home_guest    = "/home/vagrant"
home_host     = "home/"
folder_apps = "#{home_guest}/apps" # add to .gitigore (don't forget this)
folder_ssh_config = "#{home_guest}/.ssh"


# init
VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # S.O.
  config.vm.box = "ubuntu/trusty64"

  # ssh config
  config.ssh.forward_agent = true
  config.ssh.insert_key    = true
  config.ssh.password      = "vagrant"

  # RAM and CPUs
  config.vm.provider :virtualbox do |vb|
    vb.memory = 2500
    vb.cpus = 2
  end
  # config.vm.provider :virtualbox do |vb|
  #   vb.customize ["modifyvm", :id, "--memory", "3000"]
  # end


  # shared folder
  config.vm.synced_folder home_host, home_guest, create: true, nfs: true

  # config.vm.network "private_network", ip: "192.168.50.4"
  config.vm.network "private_network", type: "dhcp"

  # network
  config.vm.network :forwarded_port, guest: 5000, host: 5000

  # copy rsa keys from host to guest
  config.vm.provision "file", source: "~/.ssh/id_rsa.pub",  destination: "#{folder_ssh_config}/id_rsa.pub"
  config.vm.provision "file", source: "~/.ssh/id_rsa",      destination: "#{folder_ssh_config}/id_rsa"
  config.vm.provision "file", source: "~/.ssh/known_hosts", destination: "#{folder_ssh_config}/known_hosts"

  # chef
  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = ["cookbooks"]

    chef.add_recipe "sc_infra"
    chef.add_recipe "install_sc_apps"

    # cookbook configs
    chef.json = {
      sc_config:{
        folder_apps:       folder_apps,
        enviroment:        enviroment,
        folder_ssh_config: folder_ssh_config,
        home_guest:        home_guest
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
