# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/xenial64"

  config.vm.provider "virtualbox" do |vb|
     vb.gui = false
     vb.memory = "2048"
  end

  $script = <<SCRIPT
     echo "**************************"
     echo "Running shell provisionner"
     apt-get update -y && apt-get upgrade -y
     apt-get remove docker docker-engine docker.io -y
     apt-get install -y apt-transport-https ca-certificates curl software-properties-common
     curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
     apt-key fingerprint 0EBFCD88
     add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) edge"
     apt-get update -y
     apt-get install -y docker-ce python-pip
     systemctl enable docker && systemctl start docker
     pip install --upgrade pip && pip install docker-compose
     wget -P /tmp https://apt.puppetlabs.com/puppet5-release-xenial.deb
     dpkg -i /tmp/puppet5-release-xenial.deb
     apt-get update -y && apt-get install -y puppet-agent
     /opt/puppetlabs/puppet/bin/gem install r10k
     cd /vagrant &&  /opt/puppetlabs/puppet/bin/r10k puppetfile install
     apt-get autoremove -y
     echo "**************************"
SCRIPT

  # Bootstrap install docker / compose and puppet
  config.vm.provision "bootstrap", type: "shell" do |s|
    s.inline = $script
  end

  # Puppet
  config.vm.provision "configuration", type: "puppet" do |puppet|
    puppet.manifests_path = "manifests"
    puppet.module_path = "modules"
    puppet.manifest_file = "init.pp"
  end

end
