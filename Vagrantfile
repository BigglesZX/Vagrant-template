#!/usr/bin/ruby

Vagrant.require_version ">= 1.9.1"
Vagrant.configure("2") do |config|

    # box setup
    config.vm.box = "ubuntu/trusty64"

    # expose HTTP ports to the local network via the host
    config.vm.network :forwarded_port, guest: 80, host: 80
    config.vm.network :forwarded_port, guest: 8000, host: 8000
    config.vm.network :private_network, ip: "10.10.10.10"

    # enable symlinks in Virtualbox shared folders
    config.vm.provider :virtualbox do |v|
        v.name = "fooproject"
        v.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
    end

    # project mount
    config.vm.synced_folder ".", "/home/vagrant/fooproject", :nfs => true

    # project-specific Salt states
    config.vm.synced_folder "./salt", "/srv/salt"

    # common project Salt states
    project_states = File.join(File.expand_path('~'), '.salt-common')
    if File.directory?(project_states)
        config.vm.synced_folder project_states, "/home/vagrant/.salt-common"
    else
        abort("Vagrant error: Please install your common Salt states from https://github.com/BigglesZX/salt-common")
    end

    # developer (environment-centric) Salt states
    developer_states = File.join(File.expand_path('~'), '.salt-dev')
    if File.directory?(developer_states)
        config.vm.synced_folder developer_states, "/home/vagrant/.salt-dev"
    else
        $stdout.write "Vagrant warning: You do not have any local developer states\n"
    end

    # provisioner setup
    config.vm.provision :salt do |salt|
        salt.run_highstate = true
        salt.minion_config = "salt/config/minion.conf"
        salt.install_type = "git"
        salt.install_args = "v2016.11.1"
    end
end
