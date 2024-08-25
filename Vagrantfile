# -*- mode: ruby -*-
# vi: set ft=ruby :

boxes = [
  { :name => "pxeserver", :box => "ubuntu/22.04", :memory => 2048, :cpus => 2, :ansible => "pxeserver.yml"},
  { :name => "pxeclient", :box => "ubuntu/22.04", :memory => 4096, :cpus => 2 },
 ]

Vagrant.configure("2") do |config|
  boxes.each do |box|
    config.vm.define box[:name] do |target|
      target.vm.hostname = box[:name]
      target.vm.box = box[:box]
      case box[:name]
       when "pxeserver"
         target.vm.network :private_network, ip: "10.0.0.20", virtualbox__intnet: "pxenet"
         target.vm.network :private_network, ip: "192.168.56.10", adapter: 3
       when "pxeclient"
        target.vm.network "private_network", ip: "10.0.0.21", virtualbox__intnet: "pxenet"
      end
      target.vm.provider "virtualbox" do |v|
        v.name = box[:name]
        v.memory = box[:memory]
        v.cpus = box[:cpus]
        v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        if box[:name] == "pxeclient"
          v.customize [
            'modifyvm', :id,
            '--nic1', 'intnet',
            '--intnet1', 'pxenet',
            '--nic2', 'nat',
            '--boot1', 'net',
            '--boot2', 'none',
            '--boot3', 'none',
            '--boot4', 'none'
            ]
          end          
      end
      if box.key?(:ansible) 
        target.vm.provision "ansible" do |ansible|
          ansible.playbook = box[:ansible]
        end
      end
    end
  end
end