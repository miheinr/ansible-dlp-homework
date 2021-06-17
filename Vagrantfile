# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "generic/centos8"

  N = 3
  (1..N).each do |machine_id|
    config.vm.define "machine#{machine_id}" do |machine|
      machine.vm.hostname = "machine#{machine_id}"
      machine.vm.network "private_network", ip: "192.168.77.#{20+machine_id}"

      if machine_id == 1
        machine.vm.network "forwarded_port", guest_ip:"127.0.0.1", guest: 80, host: 8080, auto_correct: true
      end

      # Only execute once the Ansible provisioner,
      # when all the machines are up and ready.
      if machine_id == N
        machine.vm.provision :ansible do |ansible|
          # Disable default limit to connect to all the machines
          ansible.limit = "all"
          ansible.compatibility_mode = "2.0"

          # set groups for server and clients
          ansible.groups = {
            "servers" => ["machine1"],
            "clients" => [],
          }
          (2..N).each do |i|
            ansible.groups["clients"].append("machine#{i}")
          end

          # set internal ip address as inventory variable
          ansible.host_vars = {}
          (1..N).each do |i|
            ansible.host_vars["machine#{i}"] = {"internal_ip" => "192.168.77.#{20+i}"}
          end
          ansible.playbook = "provisioning/playbook.yml"
        end
      end
    end
  end

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

end
