# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/xenial64"
    config.ssh.insert_key = false
    config.vbguest.auto_update = false
	config.vm.provision "docker"

    config.vm.define "master" do |master_config|
        master_config.vm.hostname = "master"
        master_config.vm.network "private_network", ip: "10.10.10.25"
        master_config.vm.provision "shell", inline: "docker swarm init --advertise-addr 10.10.10.25 --listen-addr 10.10.10.25:2377 && docker swarm join-token --quiet worker > /vagrant/token"
        master_config.vm.provider "virtualbox" do |vb|
            vb.name = "master"
            opts = ["modifyvm", :id, "--natdnshostresolver1", "on"]
            vb.customize opts
            vb.memory = "1024"
            vb.cpus = 2
        end
    end

    (1..2).each do |i|
        config.vm.define "node-#{i}" do |node_config|
            node_config.vm.hostname = "node-#{i}"
            node_config.vm.network "private_network", ip: "10.10.10.2#{i}"
#           node_config.vm.network "forwarded_port", guest: 80, host: "808#{i}" 
            node_config.vm.provider "virtualbox" do |vb|
                vb.name = "node-#{i}"
                opts = ["modifyvm", :id, "--natdnshostresolver1", "on"]
                vb.customize opts
                vb.memory = 512
                vb.cpus = 2
            end
            node_config.vm.provision "shell", inline: "docker swarm join --advertise-addr 10.10.10.2#{i} --listen-addr 10.10.10.2#{i}:2377 --token $(cat /vagrant/token) 10.10.10.25:2377"
        end
    end 

end