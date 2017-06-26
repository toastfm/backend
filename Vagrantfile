Vagrant.configure(2) do |config|
    config.vm.box = "ubuntu/trusty64"

    config.vm.network :private_network, ip: '10.85.199.22'
    config.vm.synced_folder '.', '/vagrant', nfs: true, :mount_options => ['rw', 'vers=3', 'tcp', 'fsc' ,'actimeo=2']

    config.vm.provision "file", source: "./ansible/dev_bashrc", destination: "/home/vagrant/.bashrc"

    config.vm.network "forwarded_port", guest: 80, host: 903
    config.vm.provision "ansible" do |ansible|
        ansible.playbook = "ansible/toastfm.yml"
        ansible.extra_vars = {
            ansible_user: 'vagrant',
            tfm_environment: 'development',
            aio_server: true
        }
        ansible.sudo = true
        ansible.groups = {
            "webs" => ["default"]
        }

        # These are handy for testing.
        #ansible.tags = 'mth-deploy'
        #ansible.verbose = 'vvv'
    end
    config.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]

        host = RbConfig::CONFIG['host_os']

        # Give VM 1/4 system memory & access to all cpu cores on the host
        if host =~ /darwin/
            cpus = `sysctl -n hw.ncpu`.to_i
            # sysctl returns Bytes and we need to convert to MB
            mem = `sysctl -n hw.memsize`.to_i / 1024 / 1024 / 4
        elsif host =~ /linux/
            cpus = `nproc`.to_i
            # meminfo shows KB and we need to convert to MB
            mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024 / 4
        else # sorry Windows folks, I can't help you
            cpus = 2
            mem = 1024
        end

        v.customize ["modifyvm", :id, "--memory", mem]
        v.customize ["modifyvm", :id, "--cpus", cpus]
    end
end

# vim:set ts=4 sw=4 et filetype=ruby:
