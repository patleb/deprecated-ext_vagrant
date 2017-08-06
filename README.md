# ExtVagrant

## Installation

$ wget -qO ~/.vagrant.d/Vagrantfile https://raw.githubusercontent.com/patleb/ext_vagrant/master/Vagrantfile

## Configuration

In your project `Vagrantfile`:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure_machines(
  db: { active: true }
)

Vagrant.configure(2) do |config|
  ...
end
```

## Defaults

```ruby
web: { active: true,  memory: '1024', hostname: 'vagrant.web'},
db:  { active: false, memory: '512',  hostname: 'vagrant.db'}
```
