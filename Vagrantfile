## Install required plugins if not already installed

required_plugins = %w(
  vagrant-hostmanager
  vagrant-librarian-puppet
)
plugins_to_install = required_plugins.select { |plugin| not Vagrant.has_plugin? plugin }
if not plugins_to_install.empty?
  puts "Installing plugins: #{plugins_to_install.join(' ')}"
  if system "vagrant plugin install #{plugins_to_install.join(' ')}"
    exec "vagrant #{ARGV.join(' ')}"
  else
    abort "Installation of one or more plugins has failed. Aborting."
  end
end

## Create modules directory if not exists

Dir.mkdir("./puppet/modules") unless Dir.exists?("./puppet/modules")

## Vagrant configuration

Vagrant.configure(2) do |config|
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  config.vm.box = 'ubuntu/trusty64'
  config.vm.hostname 'springvagrant.box'
  config.vm.network :private_network, ip: '10.0.0.10'
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.synced_folder "./project", "/home/vagrant/project"
  config.vm.synced_folder "~/.m2/", "/home/vagrant/.m2"
  config.vm.provider :virtualbox do |vb|
    vb.memory = 1024
    vb.cpus = 2
    vb.name = "vagrantPuppetSpring"
  end
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"
  config.vm.provision 'shell', :inline => <<-SHELL
    apt-get update
    apt-get install -y puppet
  SHELL
  config.librarian_puppet.puppetfile_dir = 'puppet'
  config.vm.provision :puppet do |puppet|
    puppet.manifests_path = './puppet/manifests'
    puppet.module_path = './puppet/modules'
    puppet.hiera_config_path = './puppet/hiera.yaml'
    puppet.options = '--verbose'
  end
end
