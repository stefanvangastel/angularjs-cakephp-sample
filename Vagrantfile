# -*- mode: ruby -*-
# vi: set ft=ruby :
 
Vagrant.configure("2") do |config|
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  src_dir = './'
  doc_root = '/vagrant_data/webroot'
  app_name = 'cake' #File.basename(File.dirname(__FILE__))
  config.vm.network :private_network, ip: "192.168.100.2" 
  config.vm.synced_folder src_dir, "/vagrant_data", :create => true, :owner=> 'vagrant', :group=>'www-data', :mount_options => ['dmode=775,fmode=775']
  #config.berkshelf.enabled = true
  File.open('Berksfile', 'w').write <<-EOS
    cookbook 'apt'
    cookbook 'php5_ppa', git: "https://github.com/yandod/php5_ppa.git", branch: "ondrej"
    cookbook 'omusubi', git: "https://github.com/yandod/omusubi.git"
  EOS
  config.vm.provision :chef_solo do |chef|
    chef.add_recipe "apt"
    chef.add_recipe "php5_ppa::from_ondrej"
    chef.add_recipe "omusubi"
    versions = {};
    # if you would use php5.3 family with fast boot,  comment out lines bellow.
    versions['php5'] = '5.5.*'
    versions['php5-mysql'] = '5.5.*'
    versions['php5-pgsql'] = '5.5.*'
    versions['php5-curl'] = '5.5.*'
    versions['php5-mcrypt'] = '5.5.*'
    versions['php5-cli'] = '5.5.*'
    versions['php5-fpm'] = '5.5.*'
    versions['php-pear'] = '5.5.*'
    versions['php5-imagick'] = '3.*'
    chef.json = {doc_root: doc_root, 'versions' => versions}
  end
 
  config.vm.provision :shell, :inline => <<-EOS
    mysql -u root --execute  "create database if not exists #{app_name}"
    cd /vagrant_data; composer update
    cd /vagrant_data; yes | ./Console/cake schema update
  EOS
 
end
