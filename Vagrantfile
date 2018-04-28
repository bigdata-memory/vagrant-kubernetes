# -*- mode: ruby -*-
# vi: set ft=ruby :

boxes = [
    {
        :name => "master",
        :host => "192.168.1.21",
        :hostname => "master",
        :mac => "..."
    },
    {
        :name => "node1",
        :host => "192.168.1.22",
        :hostname => "node1",
        :mac => "..."
    },
    {
        :name => "node2",
        :host => "192.168.1.23",
        :hostname => "node2",
        :mac => "..."
    }
]

VAGRANTFILE_API_VERSION = "2"

$disksize = 500

$prep_script = <<SCRIPT
echo Starting provisioning...
sudo apt-get -y update
# suppress the grub interactive selection
sudo DEBIAN_FRONTEND=noninteractive apt-get -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" upgrade
sudo apt-get -y autoremove
SCRIPT

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.ssh.insert_key = false
  #config.vm.box = "generic/ubuntu1710" # it is a problematic image
#  config.vm.provision "shell", privileged: false, inline: $prep_script,
#    env: {"PRVS_USERNAME" => $username, "PRVS_VAGPKGURL" => $vagpkgurl, "PRVS_VAGPKGNAME" => $vagpkgname, "PRVS_DISKSIZE" => $disksize}

  boxes.each do |opts|
    config.vm.define opts[:name] do |boxconfig|
      boxconfig.vm.hostname = opts[:hostname]
      #boxconfig.vm.box = "debian/jessie64"
      #boxconfig.vm.box = "centos/7"
      boxconfig.vm.box = "generic/ubuntu1604"
      #boxconfig.vm.network :private_network, :ip => opts[:host]
      boxconfig.vm.synced_folder ".", "/vagrant", disabled: true
      boxconfig.vm.provision "shell", inline: <<-SHELL
        test -e /usr/bin/python || (apt-get -qqy update && apt-get install -qqy python-minimal)
      SHELL
      boxconfig.vm.provision "ansible" do |anb|
        anb.verbose = "v"
        anb.playbook = "pb.yml"
      end
      boxconfig.vm.provider :libvirt do |domain|
        domain.memory = 4096
        domain.cpus = 10
        domain.nested = true
        domain.cpu_mode = "host-model"
        domain.management_network_name = 'vagrant-libvirt-new'
        domain.management_network_address = '192.168.124.0/24'
        domain.machine_virtual_size = $disksize
      end
    end
  end
end
