# vi: set ft=ruby :

NUM_ZOOKEEPERS = 1
NUM_MASTERS    = 1
NUM_SLAVES     = 1

groups = {
  "zookeepers" => (1..NUM_ZOOKEEPERS).map { |id|
    "zookeeper%02d" % [id]
  },
  "masters" => (1..NUM_MASTERS).map { |id|
    "master%02d" % [id]
  },
  "slaves" => (1..NUM_SLAVES).map { |id|
    "slave%02d" % [id]
  },
}

Vagrant.configure(2) do |config|
  config.vm.box = "debian/jessie64"
  config.vm.network "private_network", type: "dhcp"

  (1..NUM_ZOOKEEPERS).each do |id|
    vm_name = "zookeeper%02d" % [id]
    config.vm.define vm_name do |config|
      config.vm.host_name = vm_name

      config.vm.provision "shell" do |shell|
        shell.privileged = false
        shell.inline = "echo %02d > id" % [id]
      end
    end
  end

  (1..NUM_MASTERS).each do |id|
    vm_name = "master%02d" % [id]
    config.vm.define vm_name do |config|
      config.vm.host_name = vm_name
    end
  end

  (1..NUM_SLAVES).each do |id|
    vm_name = "slave%02d" % [id]
    config.vm.define vm_name do |config|
      config.vm.host_name = vm_name

      if id == NUM_SLAVES
        config.vm.provision "ansible" do |ansible|
          ansible.playbook = "playbook/playbook.yml"
          ansible.limit = "all"
          ansible.groups = groups
          ansible.extra_vars = {
              user: "vagrant",
          }
          ansible.sudo = true
        end
      end
    end
  end
end
