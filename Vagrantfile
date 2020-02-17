# _*_ mode: ruby _*_
# vi: set ft=ruby
Vagrant.configure("2") do |config|
    config.vm.box = "centos/7"
    
    config.vm.define "node1" do|node1|
	node1.vm.hostname = "node1"
    
	config.vm.provider "virtualbox" do |node1|
	    node1.name = "node1"
	    node1.cpus = 2
	    node1.memory = "1024"
	end

    node1.vm.provision "shell", path: "script.sh"

    end
end