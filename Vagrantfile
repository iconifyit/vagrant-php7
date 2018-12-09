# -*- mode: ruby -*-
# vi: set ft=ruby :


# ======================================================================================================
# USAGE:
# ======================================================================================================

# Change config.vm.synced_folder to "/path/to/source/code", "/path/to/root/on/vm"
# Change config.vm.network "private_network", ip: "192.168.11.12" to whatever unique local IP you want
# See bootstrap.sh for more instructions
#
# You will also need to add a hosts entry on your local machine.
# 1. sudo vi /etc/hosts
# 2. Create a new line at the end of the hosts file
# 3. Enter `192.168.11.12 yourdomain.vm` (or whatever IP address you specified in the vagrant file)

# ======================================================================================================

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

    config.vm.box = "v0rtex/xenial64"

    # Mount shared folder using NFS
    config.vm.synced_folder "/Users/scott/github/iconify", "/vagrant",
        id: "core",
        :nfs => true,
        :mount_options => ['nolock,vers=3,udp,noatime']

    # Do some network configuration
    config.vm.network "private_network", ip: "192.168.11.12"

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

end