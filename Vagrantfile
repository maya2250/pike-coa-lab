# -*- mode: ruby -*-
# vi: set ft=ruby :

servers=[
  {
    :hostname => "coa-lab",
    :box => "ubuntu/xenial64",
    :ram => 8192,
    :cpu => 2,
    :script => "bash /vagrant/virtualbox-guest-setup.sh"
  }
]

Vagrant.configure("2") do |config|
  servers.each do |machine|
    config.vm.define machine[:hostname] do |node|
      node.vm.box = machine[:box]
      node.vm.hostname = machine[:hostname]
      node.vm.network "forwarded_port", guest: 80, host: 8080
      node.vm.network "forwarded_port", guest: 6080, host: 6080
      node.vm.provider "virtualbox" do |vb|
        vb.customize ["modifyvm", :id, "--memory", machine[:ram], "--cpus", machine[:cpu]]
        vb.customize ["modifyvm", :id, "--nic2", "natnetwork", "--nat-network2", "ProviderNetwork", "--nicpromisc2", "allow-all"]
        disk = File.realpath( "." ).to_s + '/openstack_data.vdi'
        vb.customize ['createhd', '--filename', disk, '--size', 50 * 1024, '--format', 'VDI'] unless File.exist? disk
        vb.customize ['storageattach', :id, '--storagectl', 'SCSI', '--type', 'hdd', '--port', 2, '--medium', disk]
      end
      node.vm.provision "shell", inline: machine[:script], privileged: true
    end
  end
end
