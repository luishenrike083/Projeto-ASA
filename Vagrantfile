# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Configuração Geral
  config.vm.box = "debian/bookworm64"
  config.ssh.insert_key = false

  # Script para parar o DHCP do VirtualBox e evitar conflito
  config.trigger.before :"Vagrant::Action::Builtin::WaitForCommunicator", type: :action do |t|
    t.warn = "Interrompe o servidor dhcp do virtualbox"
    t.run = {inline: "vboxmanage dhcpserver stop --interface vboxnet0"}
  end

  config.vm.provider :virtualbox do |v|
    v.memory = 512
    v.linked_clone = true
    v.check_guest_additions = false
  end

  # --- Servidor de Arquivos (DNS/DHCP/NFS) ---
  config.vm.define "arq" do |arq|
    arq.vm.hostname = "arq.luis.marcelino.devops"
    arq.vm.network :private_network, ip: "192.168.56.140"
    
    # Discos extras para LVM
    (1..3).each do |x|
      arq.vm.disk :disk, size: "10GB", name: "disk-#{x}"
    end

    # Chamada única para o playbook mestre
    arq.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "playbook/arq_main.yml"
    end
  end

  # --- Servidor de Banco de Dados ---
  config.vm.define "db" do |db|
    db.vm.hostname = "db.luis.marcelino.devops"
    db.vm.network :private_network, mac: "0800273A0001", type: "dhcp"
    
    db.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "playbook/db_server.yml"
    end
  end

  # --- Servidor de Aplicação ---
  config.vm.define "app" do |app|
    app.vm.hostname = "app.luis.marcelino.devops"
    app.vm.network :private_network, mac: "0800273A0002", type: "dhcp"
    app.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "playbook/app_server.yml"
    end
  end

  # --- Host Cliente ---
  config.vm.define "cli" do |cli|
    cli.vm.hostname = "cli.luis.marcelino.devops"   
    cli.vm.network :private_network, type: "dhcp"
    
    cli.vm.provider "virtualbox" do |v|
      v.memory = 1024 
    end
    cli.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "playbook/client_workstation.yml"
    end
  end
end
