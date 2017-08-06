# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'socket'

module Vagrant
  @default_machines = {
    web: { active: true,  memory: '1024', hostname: 'vagrant.web'},
    db:  { active: false, memory: '512',  hostname: 'vagrant.db'}
  }

  def self.configure_machines(machines = {})
    @default_machines.each do |type, values|
      values.merge(machines[type] || {}).each do |name, value|
        accessor_name = name == :active ? :"#{type}?" : :"#{type}_#{name}"
        define_singleton_method accessor_name do
          value
        end
      end
    end
  end

  def self.free_ip
    network_file = '.vagrant/network'
    network = File.exist?(network_file) ? File.readlines(network_file).first.strip : ''
    networks = [''].concat Socket.getifaddrs.map{ |i| i.addr.ip_address.sub(/\.\d+/, '') if i.addr.ipv4? }.compact
    loop do
      break unless networks.include?(network)
      network = "192.168.#{rand(4..254)}"
    end
    File.open(network_file, 'w') { |f| f.write(network) }
    "#{network}.#{rand(2..254)}"
  end
end

Vagrant.configure(2) do |config|
  config.vm.box = 'bento/ubuntu-16.04'

  config.ssh.forward_agent = true

  if Vagrant.has_plugin? 'vagrant-hostmanager'
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
  end

  if Vagrant.has_plugin? 'vagrant-cachier'
    config.cache.scope = :box
    config.cache.synced_folder_opts = {
      owner: "_apt",
      group: "vagrant"
    }
  end

  if Vagrant.web?
    config.vm.define :web, primary: true do |node|
      node.vm.hostname = Vagrant.web_hostname
      node.vm.network :private_network, ip: Vagrant.free_ip

      config.vm.provider :virtualbox do |vb|
        vb.memory = Vagrant.web_memory
      end
    end
  end

  if Vagrant.db?
    config.vm.define :db do |node|
      node.vm.hostname = Vagrant.db_hostname
      node.vm.network :private_network, ip: Vagrant.free_ip

      config.vm.provider :virtualbox do |vb|
        vb.memory = Vagrant.db_memory
      end
    end
  end
end
