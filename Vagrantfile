# -*- mode: ruby -*-
# vi: set ft=ruby :

vagrant_api     = "2"
                
server_ip       = "192.168.5.21"
server_memory   = "2048" # MB
server_cpus     = "2"
server_name     = "unnamed" # CHANGE THIS

# Provisioning shell scripts
$base_script = <<SCRIPT
#!/usr/bin/env bash

echo ">>> Updating repositories"
sudo apt-get update

echo ">>> Installing base packages"

# -qq implies -y --force-yes
sudo apt-get install -qq curl unzip git-core ack-grep 
sudo apt-get install -qq g++
SCRIPT

$nodejs_script = <<SCRIPT
#!/usr/bin/env bash

echo ">>> Configuring Prerequisites"
curl -sL https://deb.nodesource.com/setup_0.12 | sudo bash -

echo ">>> Installing nodejs"
sudo apt-get install -qq nodejs

echo ">>> Updating npm"

sudo npm install -g npm

SCRIPT

$iojs_script = <<SCRIPT
#!/usr/bin/env bash

echo ">>> Configuring Prerequisites"
curl -sL https://deb.nodesource.com/setup_iojs_1.x | sudo bash -

echo ">>> Installing iojs"
sudo apt-get install -qq iojs

echo ">>> Updating npm"
sudo npm install -g npm

SCRIPT

$npm_packages_script = <<SCRIPT
#!/usr/bin/env bash

# TODO - Determine which packages need to be globally installed to support common builds
echo "Installing global node packages"
sudo npm install -g grunt grunt-cli bower less

SCRIPT

Vagrant.configure(vagrant_api) do |config|
  config.vm.box = "chef/ubuntu-14.04"
  config.vm.hostname = server_name
  config.vm.network "private_network", ip: server_ip

  config.vm.provider :virtualbox do |vb|

    vb.name = server_name

    vb.customize ["modifyvm", :id, "--memory", server_memory]
    vb.customize ["modifyvm", :id, "--cpus", "2"]
    vb.customize ["modifyvm", :id, "--ioapic", "on"]

    # Set the timesync threshold to 10 seconds, instead of the default 20 minutes.
    # If the clock gets more than 15 minutes out of sync (due to your laptop going
    # to sleep for instance, then some 3rd party services will reject requests.
    vb.customize ["guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 10000]

  end

  ["vmware_fusion", "vmware_workstation"].each do |vmware|
    config.vm.provider vmware do |v|

      v.vmx['displayname']  = server_name
      v.vmx["memsize"]      = server_memory
      v.vmx["numvcpus"]     = server_cpus

    end
  end

  # Install Base Packages
  config.vm.provision :shell, :inline => $base_script

  # Uncomment either nodejs or iojs, but not both
  # Install nodejs (0.12.0)
  config.vm.provision :shell, :inline => $nodejs_script

  # Install iojs (1.x)
  # config.vm.provision :shell, :inline => $iojs_script

  # Install node packages
  config.vm.provision :shell, :inline => $npm_packages_script
end
