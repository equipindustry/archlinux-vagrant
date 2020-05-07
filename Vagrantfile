# -*- mode: ruby -*-
# vi: set ft=ruby :

# Specify minimum Vagrant version and Vagrant API version
Vagrant.require_version ">= 1.6.0"
VAGRANTFILE_API_VERSION = "2"

require 'getoptlong'

# Require YAML module
require 'yaml'

environment = ENV['ENVIRONMENT']
path_file_os = "provision/ruby/os.rb"
load path_file_os if File.exist?(path_file_os)

if environment.nil? || environment == ''
		environment = 'dev'
end

# Read YAML file with box details
settings = YAML.load_file("provision/servers/#{environment}.yaml")

# Create boxes
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

	settings['servers'].each do |servers|
		config.vm.define servers["name"] do |srv|
			srv.vm.box = servers["box"]
			srv.vm.box_url = servers["url"]

      # Network
      srv.vm.network :private_network, ip: servers["ip"]
      srv.vm.network "forwarded_port", guest: 8000, host: 8080
      srv.vm.hostname = servers["hostname"]
      srv.vm.synced_folder "./","/home/vagrant/archlinux-vagrant", {:mount_options => ['dmode=777','fmode=777']}

      # Check for updates only on `vagrant box outdated`
      srv.vm.box_check_update = true

      # stopsap may take time
      srv.vm.graceful_halt_timeout = 600

      srv.vm.provider :virtualbox do |vb|
	      vb.name = servers["name"]
	      vb.memory = servers["ram"]
	      vb.cpus = 1
	      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
	      vb.customize ["modifyvm", :id, "--ioapic", "on"]
	      vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
      end

		end
	end
end

load "Vagrantfile.local" if File.exist?("Vagrantfile.local")
