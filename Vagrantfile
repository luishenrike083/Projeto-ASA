# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
	# General Vagrant VM configuration.
	config.vm.box = "debian/bookworm64"
	config.ssh.insert_key = false

	config.trigger.before :"Vagrant::Action::Builtin::WaitForCommunicator", type: :action do |t|
		t.warn = "Iterrompe o servidor dhcp do virtualbox"
		t.run = {inline: "vboxmanage dhcpserver stop --interface vboxnet0"}
	end

	config.vm.provider :virtualbox do |v|
		v.memory = 512
		v.linked_clone = true
		v.check_guest_additions = false
	end

	# File server.
	config.vm.define "arq" do |arq|
		arq.vm.hostname = "arq.luis.marcelino.devops"
		arq.vm.network :private_network, ip: "192.168.56.140"
		(1..3).each do |x|
			arq.vm.disk :disk, size: "10GB", name: "disk-#{x}"
		end
		arq.vm.provision "ansible" do |ansible|
			ansible.compatibility_mode = "2.0"
			ansible.playbook = "playbooks/conf-dhcp.yml"
		end
	end

	# Database server.
	config.vm.define "db" do |db|
		db.vm.hostname = "db.luis.marcelino.devops"
		db.vm.network :private_network, mac: "0800273A0001", type: "dhcp"
	end

	# Application server.
	config.vm.define "app" do |app|
		app.vm.hostname = "app.luis.marcelino.devops"
		app.vm.network :private_network, mac: "0800273A0002", type: "dhcp"
	end

	# Host Cliente (cli).
	config.vm.define "cli" do |cli|
		cli.vm.hostname = "cli.luis.marcelino.devops"    
		cli.vm.network :private_network, type: "dhcp"
  		cli.vm.provider "virtualbox" do |v|
			v.memory = 1024 
		end
	end
end
