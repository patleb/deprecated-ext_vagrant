# -*- mode: ruby -*-
# vi: set ft=ruby :

# https://www.vagrantup.com/docs/vagrantfile/#lookup-path

module Vagrant
  def self.add_machines(machines)
    machines.each do |type, values|
      type_instance_name = :"@#{type}"
      if instance_variable_defined? type_instance_name
        values.merge! instance_variable_get(type_instance_name)
      end

      values.each do |name, value|
        accessor_name = name == :active ? :"#{type}?" : :"#{type}_#{name}"
        define_singleton_method accessor_name do
          value
        end
      end
    end
  end

  add_machines(
    web: { active: true,  memory: '1024', hostname: 'vagrant.web'},
    db:  { active: false, memory: '512',  hostname: 'vagrant.db'}
  )

  def self.free_ip
    loop do
      network = "192.168.#{rand(4..254)}"
      return "#{network}.#{rand(2..254)}" unless `ifconfig`.include?(network)
    end
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
