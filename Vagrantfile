# -*- mode: ruby -*-
# vi: set ft=ruby :
###VAGRANTFILE_API_VERSION = "2"

# PROJECT VARIABLES
project_name = "yardslr"
ip_address = "192.168.100.102"

Vagrant.configure(2) do |config|
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  # Private networking, automatically adds entry to host's /etc/hosts
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.vm.define project_name do |node|
    node.vm.hostname = project_name + ".local"
    node.vm.network :private_network, ip: ip_address
    node.hostmanager.aliases = [ "auctionslr.local", "consignslr.local", "estateslr.local", "fleamarkets.local", "pawnslr.local", "reslr.local", "secondslr.local", "tagsellit.local" ]
  end
  config.vm.provision :hostmanager

  # Map this entire repo to /var/www/yardslr/
  # Uses NFS so it modifies your /etc/exports
  config.vm.synced_folder "./", "/var/www/" + project_name + "/", type: "nfs"

  # Give it enough memory, but prevent maxing out host CPU
  config.vm.provider "virtualbox" do |v|
    v.name = project_name
    v.customize ["modifyvm", :id, "--cpus", "1"]
    v.customize ["modifyvm", :id, "--cpuexecutioncap", "85"]
    v.customize ["modifyvm", :id, "--memory", "1024"]
  end

  # Setup provisioning with an ansible playbook
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "provisioning/site.yml"
    ansible.groups = {
      "web" => [project_name],
      "database" => [project_name],
      "memcache" => [project_name],
      "drush" => [project_name]
    }
    ansible.extra_vars = {
      "project_name" => project_name
    }
  end
end
