# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "generic/centos8"

  # N is the number of machines (server and clients)
  N = 3
  # the first machine will be the server

  (1..N).each do |machine_id|
    config.vm.define "machine#{machine_id}" do |machine|
      machine.vm.hostname = "machine#{machine_id}"
      machine.vm.network "private_network", ip: "192.168.77.#{20+machine_id}"

      if machine_id == 1
        machine.vm.network "forwarded_port", host_ip:"127.0.0.1", guest: 80, host: 8080, auto_correct: true
      end

      # Only execute once the Ansible provisioner,
      # when all the machines are up and ready.
      if machine_id == N
        machine.vm.provision :ansible do |ansible|
          # Disable default limit to connect to all the machines
          ansible.limit = "all"
          ansible.compatibility_mode = "2.0"

          # If we specify the requirements file here then on every provisioning
          # the roles and collections will be fetched again. This is not what
          # we want.
          # ansible.galaxy_role_file = "provisioning/requirements.yml"
          # Instead use on the command line (once):
          # ansible-galaxy install -r provisioning/requirements.yml

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
end
