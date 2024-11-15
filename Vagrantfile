Vagrant.configure("2") do |config|
  config.vagrant.plugins = "vagrant-libvirt"
  # Vagrantfile for multiple VMs with static IPs using vagrant-libvirt

  ACTIVE_VMS = ["vm-1", "vm-2", "vm-3", "vm-4"]
  # ACTIVE_VMS = ["vm-1"]

  vm_settings = {
    "vm-1" => { 
      ip: "192.168.122.2",
      gateway: "192.168.122.1",
      memory: 512, 
      cpus: 1, 
      box: "pakosaan/fedora-cloud-base-41",
      os_type: "fedora"
    },
    "vm-2" => { 
      ip: "192.168.122.3", 
      gateway: "192.168.122.1",
      memory: 512, 
      cpus: 1, 
      box: "pakosaan/fedora-cloud-base-41",
      os_type: "fedora"
    },
    "vm-3" => { 
      ip: "192.168.122.4", 
      gateway:"192.168.122.1",
      memory: 512, 
      cpus: 1, 
      box: "pakosaan/fedora-cloud-base-41",
      os_type: "fedora"
    },
    "vm-4" => {
      ip: "192.168.122.5",
      gateway: "192.168.122.1",
      memory: 512,
      cpus: 1,
      box: "cloud-image/ubuntu-24.04",
      os_type: "ubuntu"
    },
  }

  def configure_vm(config, name, settings)
    config.vm.define name do |vm_config|
      vm_config.vm.box = settings[:box]
      vm_config.vm.hostname = name
      vm_config.vm.network "private_network", ip: settings[:ip], libvirt__network_name: "default"
      vm_config.vm.synced_folder "data", "/vm/data"

      vm_config.vm.provider "libvirt" do |libvirt|
        libvirt.memory = settings[:memory]
        libvirt.cpus = settings[:cpus]
      end

      # Provisioning ONLY for Fedora VMs
      if settings[:os_type] == "fedora"
        vm_config.vm.provision "shell", inline: <<-SHELL
          if ! which python3-libdnf5 > /dev/null 2>&1; then
            sudo dnf install -y python3-libdnf5
          fi
          # Check if the IP address is already set, and configure only if necessary
          CURRENT_IP=$(nmcli -g IP4.ADDRESS connection show 'Wired connection 2' | awk -F/ '{print $1}')
          if [ "$CURRENT_IP" != "#{settings[:ip]}" ]; then
            sudo nmcli connection modify 'Wired connection 2' ipv4.method manual ipv4.addresses #{settings[:ip]}/24
            sudo nmcli connection modify "Wired connection 2" ipv4.gateway #{settings[:gateway]}
            sudo nmcli connection modify "Wired connection 2" ipv4.dns "1.1.1.1,1.0.0.1"
            sudo nmcli connection up "Wired connection 2"
          fi
        SHELL
      end
    end
  end

  # Activate only specified VMs
  vm_settings.each do |name, settings|
    configure_vm(config, name, settings) if ACTIVE_VMS.include?(name)
  end
end

