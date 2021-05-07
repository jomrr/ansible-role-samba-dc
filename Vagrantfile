# -*- mode: ruby -*-
# vi: set ft=ruby :

# ip subnet for private network
SUBNET = "192.168.200"

Vagrant.configure("2") do |config|

    config.vm.box = "debian/bullseye64"
    # disable synced folder
    config.vm.synced_folder '.', '/vagrant', disabled: true

    config.vm.provider "virtualbox" do |vb|
        vb.memory = 2048
        vb.cpus = 2
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "off"]
        vb.customize ["modifyvm", :id, "--natdnsproxy1",        "off"]
    end # end provider virtualbox

    # first dc
    config.vm.define "dc1", primary: true do |dc1|
        dc1.vm.hostname = "dc1"
        dc1.vm.network "private_network", ip: "#{SUBNET}.11"
    end # end dc1

    # second dc
    config.vm.define "dc2", autostart: false do |dc2|
        dc2.vm.hostname = "dc2"
        dc2.vm.network "private_network", ip: "#{SUBNET}.12"
    end # end dc2

    # first domain member / fileserver
    config.vm.define "fs1", autostart: false do |fs1|
        fs1.vm.hostname = "fs1"
        fs1.vm.network "private_network", ip: "#{SUBNET}.21"
    end # end dc2

    # run script provisioner on all nodes for basic components.
    # os-release is missing in this box, so ansible_distribution is undefined.
    config.vm.provision "shell", inline: "
        apt-get -y update
        apt-get -y dist-upgrade
        apt-get -y install systemd systemd-sysv procps python3 python3-apt"

    # run ansible on all nodes
    config.vm.provision "ansible" do |ansible|
        ansible.galaxy_role_file = "requirements.yml"
        ansible.playbook = "test/main.yml"
        ansible.groups = {
            "dcs" => ["dc[1:2]"],
            "members" => ["fs1"],
            "all:vars" => {
                "common_role_enabled" => true,
                "hosts_role_enabled" => true,
                "hosts_ip_static" => true,
                "hosts_domain" => "samdom.localdomain",
                "sysctl_role_enabled" => true,
                "samba_role_enabled"  => false,
                "samba_interface" => ['lo', 'eth1']
            },
            "dcs:vars" => {
                "samba_role" => 'domain controller',
                "samba_use_rfc2307" => true
            },
            "members:vars" => {
                "samba_role" => 'member'
            }
        }
        ansible.host_vars = {
            "dc1" => {
                "samba_" => 80,
                "maxRequestsPerChild" => 808
            }
        }
        # ansible.raw_arguments  = [
        #     "-vvv",
        # ]
    end # end ansible provisioner

end