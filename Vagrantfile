# -*- mode: ruby -*-
# vi: set ft=ruby :

cluster = {
  "spark-master1" => { :ip => "192.168.100.50", :cpus => 1, :mem => 1024 },
  "spark-slave1" => { :ip => "192.168.100.51", :cpus => 1, :mem => 1024 },
  "spark-slave2" => { :ip => "192.168.100.52", :cpus => 1, :mem => 1024 },
  "spark-slave3" => { :ip => "192.168.100.53", :cpus => 1, :mem => 1024 },
}
Vagrant.configure(2) do |config|
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true

  cluster.each_with_index do |(hostname, info), index|
    config.vm.define hostname do |cfg|
	  
	  cfg.vm.provider :virtualbox do |vb, override|
	    override.vm.box = "ubuntu/trusty32"
		override.vm.box_url = "https://vagrantcloud.com/ubuntu/trusty32"
	    override.vm.network :private_network, ip: "#{info[:ip]}"
		override.vm.hostname = hostname

        vb.name = 'vagrant-' + hostname
		vb.customize ["modifyvm", :id, "--memory", info[:mem], "--cpus", info[:cpus], "--hwvirtex", "on"]
      end	

      # provision nodes with ansible
      if index == cluster.size - 1
        cfg.vm.provision :ansible do |ansible|
          ansible.verbose = "vvvv"

          ansible.inventory_path = "hosts"
          ansible.playbook = "spark-cluster.yml"
          ansible.limit = 'all'# "#{info[:ip]}" # Ansible hosts are identified by ip
		  ansible.sudo = true
		  ansible.extra_vars = { ansible_ssh_user: 'vagrant'}
        end # end provision
      end #end if

    end # end config

  end #end cluster

end #end vagrant

