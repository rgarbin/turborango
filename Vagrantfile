# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure('2') do |config|
  config.vm.box      = 'precise32'
  config.vm.box_url  = 'http://files.vagrantup.com/precise32.box'
  config.vm.hostname = 'rails-dev-box'

  config.vm.provider 'parallels' do |v, override|
    override.vm.box = 'parallels/ubuntu-12.04'
    override.vm.box_url = 'https://vagrantcloud.com/parallels/ubuntu-12.04'

    # Can be running at background, see https://github.com/Parallels/vagrant-parallels/issues/39
    v.customize ['set', :id, '--on-window-close', 'keep-running']
  end

  config.vm.network :forwarded_port, guest: 3000, host: 4000

  config.vm.provision :puppet do |puppet|
    puppet.manifests_path = 'puppet/manifests'
    puppet.module_path    = 'puppet/modules'
  end

  module Provision
    PATH = "/var/opt/vagrant_provisioner"

    APP = <<-PROVAPP
      echo "Provisioning application..."
      echo "#Add RVM to PATH for scripting"
      export $PATH=$PATH:/home/vagrant/.rvm/bin
      source /home/vagrant/.rvm/scripts/rvm
      cd /vagrant
      echo "bundle ..."
      bundle check || bundle
      echo "create DB ..."
      rake db:create
      echo "Running migrations ..."
      rake db:migrate
      set -e
      echo "Seeding DB ..."
      rake db:seed
      echo "starting server ...."
      rails s -d
    PROVAPP
  end

  config.vm.provision :shell, :inline => "mkdir -p #{Provision::PATH}"
  config.vm.provision :shell, :inline => Provision::APP

end
