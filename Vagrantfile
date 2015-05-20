# -*- mode: ruby -*-
# vi: set ft=ruby :
# ;

# Copyright 2015 Cisco Systems
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

#------------------------------------------------------------------------------
# Authors
#
# David Lapsley (dlapsley@cisco.com)
# Anthony Rogliano (aroglian@cisco.com)

#------------------------------------------------------------------------------
# Getting Started
#
# Pre-requisites
#
# * Assumes you are running Mac OSX (will likely work with other UNICES.
# * Assumes you have the following installed:
#     * Virtualbox: https://www.virtualbox.org
#     * Vagrant: https://www.vagrantup.com
#     * Ansible: http://docs.ansible.com/intro_installation.html
# * Assumes you have the following vagrant plugins installed:
#     * Vagrant Cachier: https://github.com/fgrehm/vagrant-cachier
#     * Vagrant Hostsupdater: https://github.com/cogitatio/vagrant-hostsupdater
#
# Instructions
#
# To use this file, modify the CLUSTER_CONFIG data structure below to suit
# your purporses. Once you have done that:
#
#         $ vagrant up                <-- creates, launches and provisions the cluster.
#                                                         If the cluster has already been provisioned but
#                                                         is halted, this will start all fo the VMs in the
#                                                         cluster. 
#         $ vagrant provision <-- (re-)provisions the cluster
#         $ vagrant destroy     <-- shutsdown and destroys the cluster VMs
#         $ vagrant ssh <nodename>
#                                                 <-- ssh-es into the specified nodename
#         $ vagrant halt            <-- halts the cluster VMs
#------------------------------------------------------------------------------

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

# Per-role memory and cpu profiles.
mhv = { :memory => 2048, :numcpus => 2}
mcp = { :memory => 2048, :numcpus => 4}
svc = { :memory => 2048, :numcpus => 4}

#------------------------------------------------------------------------------
# Cluster configuration
#------------------------------------------------------------------------------
CLUSTER_CONFIG = {
    # Subdirectory that will contain the ansible files listed below.
    :ansible_subdir => 'ansible',

    # Location to store/read the ansible inventory file.
    :ansible_inventory_file => 'ansible/inventory.txt',

    # Location of main ansible playbook.
    :ansible_playbook => 'ansible/site.yml',

    # Specificy which OS to use (global).
    # :os => "shrink0r/ubuntu-trusty-server-x64",
    :os => "vmware/trusty64",
    # :os => "hashicorp/precise64",

    # Specify which provider to use (global).
    # :provider => "virtualbox",
    :provider => "vmware_fusion",

    # Specify the last hostname, so that we can run ansible at the appropriate
    # time. It should match the last host in the list below.
    :last_hostname => "mhv2.dav1",

    # A hash that keys off of hostname and contains the configuration for each
    # server in the virtual cluster.
    :hosts => {
        # A server entry. Server role is decided based on the hostname. There are
        # currently three server roles/classes:
        #     controller -> /mcp[0-9+].*/
        #     hypervisor -> /mcp[0-9+].*/
        #     servicenode -> Any node that is neither a controller nor hypervisor.
        "mcp1.dav1" => {
                # RAM to allocate for this server.
                :memory => 2048,

                # Number of virtual cpus to allocate for this server.
                :numcpus => 2,

                # IP address to use for the service interface for this serer. This
                # will be eth1. eth0 is assigned by vagrant and will be a NAT address
                # that can acccess the external Internet via the host machine's
                # primary network interface.
                :service_ip => "10.0.1.60",
        },
        "mcp2.dav1" => {
                # RAM to allocate for this server.
                :memory => 2048,

                # Number of virtual cpus to allocate for this server.
                :numcpus => 2,

                # IP address to use for the service interface for this serer. This
                # will be eth1. eth0 is assigned by vagrant and will be a NAT address
                # that can acccess the external Internet via the host machine's
                # primary network interface.
                :service_ip => "10.0.1.61",
        },
        "mcp3.dav1" => {
                # RAM to allocate for this server.
                :memory => 2048,

                # Number of virtual cpus to allocate for this server.
                :numcpus => 2,

                # IP address to use for the service interface for this serer. This
                # will be eth1. eth0 is assigned by vagrant and will be a NAT address
                # that can acccess the external Internet via the host machine's
                # primary network interface.
                :service_ip => "10.0.1.62",
        },
        "mhv1.dav1" => {
                # RAM to allocate for this server.
                :memory => 2048,

                # Number of virtual cpus to allocate for this server.
                :numcpus => 2,

                # IP address to use for the service interface for this serer. This
                # will be eth1. eth0 is assigned by vagrant and will be a NAT address
                # that can acccess the external Internet via the host machine's
                # primary network interface.
                :service_ip => "10.0.1.63",
        },
        "mhv2.dav1" => {
                # RAM to allocate for this server.
                :memory => 2048,

                # Number of virtual cpus to allocate for this server.
                :numcpus => 2,

                # IP address to use for the service interface for this serer. This
                # will be eth1. eth0 is assigned by vagrant and will be a NAT address
                # that can acccess the external Internet via the host machine's
                # primary network interface.
                :service_ip => "10.0.1.64",
        }
    },
    :groups => {
        "controllers" => ["mcp1.dav1", "mcp2.dav1", "mcp3.dav1"],
        "hypervisors" => ["mhv1.dav1", "mhv2.dav1"],
        "all" => ["mcp1.dav1", "mcp2.dav1", "mcp3.dav1", "mhv1.dav1",
                  "mhv2.dav1"]
    }
}


#------------------------------------------------------------------------------
# Create empty Ansible playbook
#------------------------------------------------------------------------------
def create_empty_playbook(cluster_config)
    # Creates an empty ansible playbook.
    #
    # :param cluster_config: A hash containing all of the cluster config info.
    open(cluster_config[:ansible_playbook], 'w') do |playbook_file|
        playbook_file.puts '---'
        playbook_file.puts '- hosts: all'
        playbook_file.puts '    tasks:'
        playbook_file.puts '        debug: msg="Empty Ansible site.yml"'
    end
end

#------------------------------------------------------------------------------
# Check if Ansible files and subdirs exist
#------------------------------------------------------------------------------
def check_ansible_files(cluster_config)
    # Check if Ansible files and subdirs exist, if not create them
    #
    # :param cluster_config: A hash containing all of the cluster config info.
    if not File.directory?(cluster_config[:ansible_subdir])
        Dir.mkdir(cluster_config[:ansible_subdir])
    end
    if not File.exist?(cluster_config[:ansible_playbook])
        create_empty_playbook(cluster_config)
    end
end

#------------------------------------------------------------------------------
# Create the ansible inventory file
#------------------------------------------------------------------------------
def create_inventory_file(cluster_config)
    # Creates an inventory file based on the cluster configuration.
    #
    # Assumes that controler names are of the form mcp[0-9]+.* and that 
    # hypervisor names are of the form mhv[0-9]+.*.
    #
    # :param cluster_config: cluster configuration hash.

    open(cluster_config[:ansible_inventory_file], 'w') do |inventory_file|
        cluster_config[:hosts].each do |hostname, host_config|
             key_file = "%s/.vagrant/machines/%s/%s/private_key" % [
                 Dir.getwd, hostname, cluster_config[:provider]]
    
             # Write host line to ansible inventory file.
             inventory_file.puts "%s ansible_ssh_host=%s ansible_ssh_user=vagrant ansible_ssh_private_key_file=%s" % [
                 hostname, host_config[:service_ip], key_file]
        end
        inventory_file.puts " "

        cluster_config[:groups].each do |group, hosts|
            # Write controllers group into inventory file.
            inventory_file.puts "[%s]" % [group.to_s]
            hosts.each do |host|
                inventory_file.puts host
            end
            inventory_file.puts " "
        end
    end
end


#------------------------------------------------------------------------------
# Main vagrant provisioning section
#------------------------------------------------------------------------------
def provision(cluster_config)
    # Provisions the vagrant cluster.
    #
    # :param cluster_config: A hash containing all of the cluster config info.
    Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
        config.ssh.forward_agent = true
    
        cluster_config[:hosts].each do |hostname, host_config|
            # Loop through each host in cluster_config.
            #
            config.vm.define hostname do |server_conf|
                # Configure an individual host.
                server_conf.vm.box = cluster_config[:os]
                server_conf.vm.host_name = hostname
                # server_conf.vm.synced_folder "cache", "/cache", owner: "root", group: "root"

                # Configure various ip interfaces.
                if host_config.key?(:service_ip)
                    server_conf.vm.network :private_network, ip: host_config[:service_ip]
                end
                if host_config.key?(:tenant_ip)
                    server_conf.vm.network :private_network, ip: host_config[:tenant_ip]
                end
                if host_config.key?(:external_ip)
                    server_conf.vm.network :private_network, ip: host_config[:external_ip]
                end
    
                # Set VM parameters.
                server_conf.vm.provider cluster_config[:provider] do |v|
                    if cluster_config[:provider] == "vmware_fusion"
                        v.vmx["memsize"] = host_config[:memory]
                        v.vmx["numvcpus"] = host_config[:numcpus]
                    elsif cluster_config[:provider] == "virtualbox"
                        v.customize ["modifyvm", :id, "--memory", host_config[:memory]]
                        v.customize ["modifyvm", :id, "--cpus", host_config[:numcpus]]
                        v.gui = true
                    else
                        puts "Invalid provider ", host_config[:provider]
                    end
                end
            end
        end

        if Vagrant.has_plugin?("vagrant-cachier")
            # Configure cached packages to be shared between instances of the same
            # base box. Only use this for a single VM or for serialized VM's.
            # Otherwise, # you need to configure separate cache directories for each
            # VM.
            config.cache.scope = :box
        end

        # jbrendel:
        #
        # By listing the last host separately, we force it to be brought up
        # last. Thus, the provisioner is run only for this last host,
        # meaning: We can guarantee that all hosts are up and running when
        # the provisioner kicks in.
        #
        last_hostname = cluster_config[:last_hostname]
        config.vm.define cluster_config[:last_hostname] do |server_conf|
            server_conf.vm.provision "ansible" do |ansible|
                ansible.playbook = cluster_config[:ansible_playbook]
                ansible.verbose = "vvvv"
                ansible.inventory_path = cluster_config[:ansible_inventory_file]
                # ansible.extra_vars = "ansible/vars/extra_vars.yml"
                ansible.host_key_checking = false
                ansible.limit = 'all'
            end
        end
    end
end

#------------------------------------------------------------------------------
# Script execution entry point.
#------------------------------------------------------------------------------
check_ansible_files(CLUSTER_CONFIG)
create_inventory_file(CLUSTER_CONFIG)
provision(CLUSTER_CONFIG)
