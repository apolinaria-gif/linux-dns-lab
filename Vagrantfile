
Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/jammy64"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024
    vb.cpus = 1
  end

  # DNS SLAVE вЂ” Р·Р°РїСѓСЃРєР°РµС‚СЃСЏ РїРµСЂРІС‹Рј
  config.vm.define "ns02" do |vm|
    vm.vm.hostname = "ns02"
    vm.vm.network "private_network",
      ip: "192.168.50.11",
      virtualbox__intnet: "dns"
  end

  # DNS CLIENT 1
  config.vm.define "client" do |vm|
    vm.vm.hostname = "client"
    vm.vm.network "private_network",
      ip: "192.168.50.15",
      virtualbox__intnet: "dns"
  end

  # DNS CLIENT 2
  config.vm.define "client2" do |vm|
    vm.vm.hostname = "client2"
    vm.vm.network "private_network",
      ip: "192.168.50.16",
      virtualbox__intnet: "dns"
  end

  # DNS MASTER + ANSIBLE CONTROL NODE вЂ” Р·Р°РїСѓСЃРєР°РµС‚СЃСЏ РїРѕСЃР»РµРґРЅРёРј
  config.vm.define "ns01" do |vm|
    vm.vm.hostname = "ns01"

    vm.vm.network "private_network",
      ip: "192.168.50.10",
      virtualbox__intnet: "dns"

    # РџРµСЂРµРґР°С‡Р° SSH-РєР»СЋС‡РµР№ РѕСЃС‚Р°Р»СЊРЅС‹С… РјР°С€РёРЅ РєРѕРЅС‚СЂРѕР»Р»РµСЂСѓ
    vm.vm.synced_folder ".vagrant/machines",
      "/vagrant/vagrant-machines"

    # РџРѕРґРіРѕС‚РѕРІРєР° SSH-РґРѕСЃС‚СѓРїР°
    vm.vm.provision "shell", inline: <<-SHELL
      set -e

      install -d -m 700 -o vagrant -g vagrant /home/vagrant/.ssh

      for host in ns01 ns02 client client2; do
        src="/vagrant/vagrant-machines/$host/virtualbox/private_key"

        if [ ! -f "$src" ]; then
          echo "SSH key not found: $src"
          exit 1
        fi

        install -m 600 -o vagrant -g vagrant \
          "$src" "/home/vagrant/.ssh/$host"
      done
    SHELL

    # Ansible Р·Р°РїСѓСЃРєР°РµС‚СЃСЏ Р’РќРЈРўР Р ns01
    vm.vm.provision "ansible_local" do |ansible|
      ansible.install = true
      ansible.become = true
      ansible.provisioning_path = "/vagrant/provisioning"
      ansible.playbook = "site.yml"
      ansible.inventory_path = "/vagrant/provisioning/inventory.ini"
      ansible.limit = "all"
      ansible.verbose = "vv"
    end
  end

end

