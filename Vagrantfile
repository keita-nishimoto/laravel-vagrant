# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.ssh.insert_key = false
  config.vm.box = "centos/7"
  config.vm.provider "virtualbox" do |vm|
    vm.customize ["modifyvm", :id, "--memory", "1024", "--cpus", "2", "--ioapic", "on", "--cableconnected1", "on"]
  end

  config.vm.define :frontend do |frontend|
    frontend.vm.hostname = "dev.laravel-front.net"
    frontend.vm.network :private_network, ip: "192.168.34.20"

    %w(
      laravel-front-sample
    ).each do |dir|
      frontend.vm.synced_folder "../#{dir}", "/home/vagrant/#{dir}", mount_options: ["dmode=777","fmode=777"] if  File.exist?("../#{dir}")
    end

    # ネットワークの再起動を行う（何故か固定IPが効かない時がある為）
    frontend.vm.provision "network-restart", type: "shell", run: "always", inline: "sudo service network restart"
    frontend.vm.provision :ansible do |ansible|
      ansible.limit = "all"
      ansible.inventory_path = "../laravel-ansible/frontend-local"
      ansible.playbook = "../laravel-ansible/site.yml"
    end
  end

  config.vm.define :backend do |backend|
    backend.vm.hostname = "dev.laravel-api.net"
    backend.vm.network :private_network, ip: "192.168.34.21"

    %w(
      laravel-api-sample
    ).each do |dir|
      backend.vm.synced_folder "../#{dir}", "/home/vagrant/#{dir}", mount_options: ["dmode=777","fmode=777"] if  File.exist?("../#{dir}")
    end

    # ネットワークの再起動を行う（何故か固定IPが効かない時がある為）
    backend.vm.provision "network-restart", type: "shell", run: "always", inline: "sudo service network restart"
    backend.vm.provision :ansible do |ansible|
      ansible.limit = "all"
      ansible.inventory_path = "../laravel-ansible/backend-local"
      ansible.playbook = "../laravel-ansible/site.yml"
    end
  end

  config.vm.define :db_master do |db_master|
    db_master.vm.hostname = "laravel-api-db.master-1"
    db_master.vm.network :private_network, ip: "192.168.34.22"

    # ネットワークの再起動を行う（何故か固定IPが効かない時がある為）
    db_master.vm.provision "network-restart", type: "shell", run: "always", inline: "sudo service network restart"
    db_master.vm.provision :ansible do |ansible|
      ansible.limit = "all"
      ansible.inventory_path = "../laravel-ansible/db-master-local"
      ansible.playbook = "../laravel-ansible/site.yml"
    end
  end
end
