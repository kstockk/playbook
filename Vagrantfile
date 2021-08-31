# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "generic/debian10"

  config.ssh.insert_key = false

  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.provider :virtualbox do |v|
    v.memory = 256
    v.linked_clone = true
  end

  config.vm.define "rasp1" do |rasp|
    rasp.vm.hostname = "rasp1.test"
    rasp.vm.network :private_network, ip: "172.16.229.8"
  end
end
