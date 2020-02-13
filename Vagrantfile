# _*_ mode: ruby _*_
# vi: set ft=ruby
Vagrant.configure("2") do |config|
    config.vm.box = "centos/7"
    
    config.vm.define "node" do|node|
	node.vm.hostname = "node"
    
	config.vm.provider "virtualbox" do |node|
	    node.name = "node"
	    node.cpus = 2
	    node.memory = "1024"
	end

    node.vm.provision "shell", path: "script.sh"

    end
end