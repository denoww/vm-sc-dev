# -*- mode: ruby -*-
# vi: set ft=ruby :

# Config
enviroment             = "development"
ruby_version_release   = "2.0.0-p247"
folder_shared_guest    = "/home/vagrant"
folder_shared_host     = "."
folder_apps = "#{folder_shared_guest}/apps" # add to .gitigore (don't forget this)
folder_ssh_config = "#{folder_shared_guest}/.ssh"


# init
VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # S.O.
  config.vm.box = "ubuntu/trusty64"

  # ssh config
  config.ssh.forward_agent = true
  config.ssh.insert_key    = true
  config.ssh.password      = "vagrant"

  # 3GB of RAM
  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "3000"]
  end

  # shared folder
  config.vm.synced_folder folder_shared_host, folder_shared_guest, nfs: true#, mount_options: ["dmode=775","fmode=775"]

  # config.vm.synced_folder "./", "/var/www", id: "vagrant-root", :nfs => false, owner: "vagrant", group: "www-data", mount_options: ["dmode=775","fmode=775"]

  config.vm.network "private_network", ip: "192.168.1.25"

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
