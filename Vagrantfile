# -*- mode: ruby -*-
# vi: set ft=ruby :


# ======================================================================================================
# LOAD SETTINGS
# ======================================================================================================

require 'yaml'
settings = YAML.load_file 'settings.yml'

# ======================================================================================================
# DO NOT MODIFY BELOW THIS LINE
# ======================================================================================================

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

    config.vm.box = "v0rtex/xenial64"

    # Mount shared folder using NFS
    # config.vm.synced_folder "/Users/scott/github", "/vagrant",
    config.vm.synced_folder settings['repo_path'], "/vagrant",
        id: "core",
        :nfs => true,
        :mount_options => ['nolock,vers=3,udp,noatime']

    # Do some network configuration
    config.vm.network "private_network", ip: settings['ip_address']

    # Assign a quarter of host memory and all available CPU's to VM
    # Depending on host OS this has to be done differently.
    config.vm.provider :virtualbox do |vb|
        host = RbConfig::CONFIG['host_os']

        if host =~ /darwin/
            cpus = `sysctl -n hw.ncpu`.to_i
            mem = `sysctl -n hw.memsize`.to_i / 1024 / 1024 / 4

        elsif host =~ /linux/
            cpus = `nproc`.to_i
            mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024 / 4

        # Windows...
        else
            cpus = 4
            mem = 2048
        end

        vb.customize ["modifyvm", :id, "--memory", mem]
        vb.customize ["modifyvm", :id, "--cpus", cpus]
    end

    config.vm.provision :shell, :path => "bootstrap.sh"

    # pass environ
    # config.vm.provision :shell, :path => "bootstrap.sh", env: {"VAR_NAME" => "variable value"}

end