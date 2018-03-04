### Notities boek [Michael Peacock - Creating Development Environments with Vagrant]

Preface

- Vagrant is a powerful tool for creating, managing and working with virtualized development environments for your projects
- By creating such an environment for each project, their dependencies and requirements are isolated and don't interfere
- With Vagrant, we can wipe that state clean if we break our environment, and be back up and running in no time

1. Getting started with Vagrant

- Instead of having to battle configurations when working on other projects, each project can have its own virtualized environment
- It can have its own dedicated web server, database server, and the versions of the programming language and other dependencies it needs
- With a virtualized environment, the development environment can also mimic the production environment
- Vagrant is a powerful development tool, which lets you manage and support the virtualization of your development environment
- Instead of running all your projects locally, Vagrant lets you run each project in its own dedicated virtual environment
- Vagrant provides a CLI and common configuration language
- Vagrant uses providers such as VirtualBox
- The entire configuration is stored in simple plain text files
- If your project's production environment involves multiple servers, Vagrant lets you emulate that with seperate virtual servers on your machine
- With Vagrant
	- Your development environment can mimix the production environment
	- Integrated provisioning tools such as Puppet and Chef allow you to store configuration in a standard format, which can also be used to update production environments
	- Each project is seperate in its own virtualized environment, so issues as a result of configuration and version differences for dependencies on different projects are things of the past
	- New team members can be onboarded to new projects as easy as git clone && vagrant up
	- The environment can act as if it was your local machine and can map the web server port (80) of your development machine to your development environment if you wish
	- You can let colleagues view your own development environmen, as well as easily share the development environment
- VirtualBox is needed in order to use Vagrant
- Vagrant has a built-in Ruby-interpreter and VirtualBox provider
- VirtualBox is a graphical program, which lets you visually create virtual machines, allocate resources, load external media such as OS CD's, and view the screen of the virtual machine
- Vagrant wraps on top of this and provides an intuitive CLI along with the integration of additional tools
- First install VirtualBox, then install vagrant

2. Managing Vagrant Boxes and Projects

- CREATING VAGRANT PROJECTS
- 1. IMPORTING AND USING BASE BOXES
- Base box = packaged version of an OS with some specific configurations in place
- Number of configurations and packages minimal
- End user should provision these
- Provided we are in the directory we wish to convert into a new Vagrant project
- Run the following command in the terminal:
	- vagrant init precise64 http://files.vagrantup.com/precise64.box
- This instructs Vagrant to create a new project with configuration to use the box named precise64 or to import the box located at that link
- Base boxes are downloaded and stored in a place Vagrant can access and reuse
- The Vagrant configuration file is written in Ruby, and the default Vagrantfile we get is primarily comments illustrating some of the ways we can customize the file
- 2. CREATING PROJECTS WITHOUT IMPORTING A BASE BOX
- Once a box has been imported, it is usable within new and existing Vagrant projects, using the name it was imported with
- For example:
	- vagrant init base64
- But even if we use a base box that is already imported to Vagrant, it doesn't provide a fallback URL
- We can also create a new Vagrant project with the following command:
	- vagrant init
- This will create a Vagrantfile but without
	- A fallback URL
	- The base box name that the project will use will be base
- 3. Managing Vagrant boxes
- Using the vagrant box subcommands
- Display them with vagrant box --help
- Four available box-related subcommands:
	- vagrant box add <name> <url> [--provider provider] [--force]
	- vagrant box list
	- vagrant box remove <name> <provider>
	- vagrant box repackage <name> <provider>
- ADDIN VAGRANT BOXES
- vagrant box add precise64 http://files.vagrantup.com/precise64.box --force
- After this command, the precise64 box is added to vagrant box list
- You can remove a vagrant box with the following command
- vagrant box remove precise64 virtualbox
- The repackage subcommand lets us convert a Vagrant environment, complete with anu customizations we have mode to it, into a box which we can reuse and distribute to others
- FINDING VAGRANT BOXES
- The Vagrant project provides a few boxes: Ubuntu Lucid and Ubuntu Precise
- However, you can also download them from other sources such as vagrantbox.es
- POWERING UP THE VIRTUAL MACHINE
- vagrant up in the folder where the vagrantfile is located
- If no previously suspended environment is found, Vagrant will perform the following:
	- Copy the base box
	- Create a new virtual machine with the relevant provider
	- Forward to any configured ports
	- Boot the VM
	- Configure and enable networking, so that we can communicate with the VM	
	- Map shared folders between the host and the guest
	- Run any provisioning tools that are set up such as Puppet, Chef, or SSH provisioning
- SUSPENDING THE VIRTUAL MACHINE
- We can save the current state of the virtual machine to disk so that we can resume it later if we run vagrant suspend
- RESUMING THE VIRTUAL MACHINE
- 	In order to resume a previously suspended virtual machine, we run vagrant resume
- SHUTTING DOWN THE VIRTUAL MACHINE
- We can shut down a runing virtual machine using the vagrant halt command
- This instructs the VM to stop all running processes and shut down
- To use it again, run vagrant up
- STARTING FROM SCRATCH
- If things go wrong, you can instruct Vagrant to destroy our virtual box and then create it again, using the configurations to set it up
- vagrant destroy
- vagrant up
- CONNECTING TO THE VIRTUAL MACHINE OVER SSH
- Run vagrant ssh to connect to the VM over SSH
- INTEGRATION BETWEEN THE HOST AND THE GUEST
- We need our own machine to be capable of integrating tightly with the guest
- PORT FORWARDING
- We may want to integrate host and guest more tightly
- One option is port forwarding, where we can tunnel a port from the VM to a port on the host machine
- Example: We have a web server running on our own machine, we don't want to map the web server port from Vagrant onto the same port, there would be a conflict
- If we map the web server port on the VM to port 8888 on the host, then visiting http://localhost:8888 on our host machine would show us the web service we run on the guest
- Port forwarding is done via lines in the Vagrantfile
- config.cm.network :forwarded_port, guest: 80, host:8888
- We can also prevent port conflicts while running multiple VM's simultaniously
- auto_correct: true
- SYNCED FOLDERS
- Allow us to share a folder between the host and the guest
- By default Vagrant shares the \vagrant folder on the VM
- You can alter this in the Vagrantfile
- config.vm.synced_folder "/Users/jens/assets" "/var/www/assets"
- We can enable the NFS on a per synced folder basis by adding a line
- :nfs => true
- NETWORKING
- If we want to share Vagrant-managed VM's directly in the network, we van attach the VM to our internal network
- config.vm.network :private_network, ip: "192.168.1.100"
- AUTO-RUNNING COMMANDS
- On of the key concepts of Vagrant is provisioning
- There are three key provisioning options:
	- SSH
	- Puppet
	- Chef
- There are two ways we can use SSH provisioning
	- Directly run a command from our Vagrantfile: config.cm.provision :shell, :inline => "sudo apt-get update"
	- Tell Vagrant to run a particular script: config.vm.provision :shell, :path => "provision.sh"

3. Provisioning with Puppet

- PROVISIONING
- The process of setting up a VM so that it can be used for a specific purpose or project.
- Provisioning takes us to the next level and gives us a fully working environment for our project
- ABOUT PUPPET
- Puppet is a provisioning tool which we can use to set up a server for use for a project
The configuration which determine show the server needs to be set up and stored within our Vagrant project and can be shared
- Information about how a server should be configured (SW, files, users and groups,...) is written into files known as the Puppet manifests
- Written by using Puppet's own language, which is a Ruby Domain Specific Language
- We will use Puppet in standalone mode, but it has also server-client capabilities
- Puppet is idempotent
- CREATING MODULES AND MANIFESTS WITH PUPPET
- Puppet is made up of a manifest file, and a number of modules
- Default manifest specifies which modules are to be used
- PUPPET CLASSES
- Puppet modules consist of classes, which in turn utilize a number of resource types to achieve a specific requirement for our server
- Example:
	- class apache {
		package { "apache2":
			ensure => present,
			require => Exec['apt-get-update']
		}
	}
- could be saved as default.pp in modules/apache/manifests
- DEFAULT PUPPET MANIFESTS
- Puppet modules are typically all located in a specific modules directory
- Because of this, we need to have a default Puppet manifest that includes a list of modules to be run, and any configurations on them
- A basic manifest, which include a module we wrote earlier:
	- import "apache"
	- include "apache"
- Example: Supervisord module is designed to be used for multiple different things which the developer using it can customize
	- supervisor::service {
		'resize_images':
			ensure => present,
			enable => true, 
			command => '/usr/bin/php /vagrant/src/pricate/index.php img_resize',
			user => 'root',
			group => 'root',
			autorestart => true,
			startsecs => 0,
			num_procs => 5,
			require => [ Package['php5-cli'], Package['beanstalkd']];
	}
	- supervisor::service {
		'email':
			ensure => present,
			enable => true,
			command => '/usr/bin/php /vagrant/src/private/index.php email',
			user => 'root',
			group => 'root',
			autorestart => true,
			startsecs => 0,
			num_procs => 5,
			require => [ Package['php5-cli'], Package['beanstalkd']];
	}
- Here we are instructing Puppet to use the supervisord module twice, to set up and manage two workers fot us
- For each worker we have a set of five processes to run
- We have to set the user and group to run them as
- We have defined PHP's command line interface and the beanstalkd worker queue as requirements for the workers
- RESOURCES
- Puppet provides a range of resource types which we can utilize to create our configuration file
- Resource types available include:
	- cron: To manage cron jobs on Linux and UNIX based systems
	- exec: To run commands at the terminal/command prompt
	- file: To manage and manipulate files and folders on the filesystem
	- group: To manage user groups
	- package: To install software
	- service: To manage running services on the machine
	- user: To manage user accounts on the machine
- RESOURCE REQUIREMENTS
- Certain things we do with Puppet will require other actions to have been performed first
- They can be defined with the require => parameter
- RESOURCE EXECUTION ORDER
- We can define stages within our default puppet manifest and the instruct Puppet to run certain classes from within that stage
- Example:
	- stage {'first': before => Stage[Main]}
	  class {'apache': stage => first}
- INSTALLING SOFTWARE
- Example: We want to install Apache on our server
	1. Update our package manager
	2. Install the Apache package
	3. Run the Apache service
1. UPDATING OUR PACKAGE MANAGER
- exec { 'apt-get update':
	command => '/usr/bin/apt-get update',
	timeout => 0	
}
2. Installing the Apache package
- package { "apache2":
	ensure => present,
	require => Exec['apt-get update']
}
3. Running the Apache service
- service { "apache2":
	ensure => running,
	require => Package['apache2']	
}
- FILE MANAGEMENT
	1. Copying a file
	2. Creating a symlink
	3. Creating folders
	4. Creating multiple folders in one go
1. COPYING A FILE
- file { 'etc/apache2/sites-available/default':
	source => '/vagrant/provision/modules/apache/files/default',
	owner => 'root',
	group => 'root',
	require => Package['apache2']
}
2. CREATING A SYMLINK
- file { '/var/www/vendor':
	ensure => 'link',
	target => '/vagrant/vendor'
	require => Package['apache2']
}
3. CREATING FOLDERS
- file { "/var/www/uploads":
	ensure => "directory",
	owner => "www-data",
	group => "www-data",
	mode => 777
}
4. CREATING MULTIPLE FOLDERS IN ONE GO
$cache_directories = [ "/var/www/cache", "/var/www/cache/pages", "/var/www/cache/routes", "/var/www/cache/templates",]
file { $cache_directories:
	ensure => "diretory",
	owner => "www-data",
	group => "www-data",
	mode => 777,
}
