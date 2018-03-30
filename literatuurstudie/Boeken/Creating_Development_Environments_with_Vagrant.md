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
- CRON MANAGEMENT
- The CRON resource type lets us use Puppet to manage CRON jobs, which we need to run on the machine
- cron { web_cron:
	command => "/usr/bin/php /vagrant/cron.php",
		user => "root"
			hour => [1-4],
				minute => [0,30],
	}

}
- RUNNING COMMANDS	
- The EXEC resource type allows us to run commands through the terminal on the machine we are provisioning
- One caveat with the EXEC command is that if you re-provision with Puppet it will re-run the command, which depending on the command could be damaging
- But, the CREATES parameter tells Puppet of a file that will be created when the command is run, if Puppet finds that file, it knows that it has already been run and won't run again
- Example: This will cause the PHP composer tool to download dependencies
- Because of the lock file that is created, we can use the CREATES parameter to prevent the command from being executed if it has previously been executed and has created the lock file
- exec { "compose":
	command => '/bin/rm -rfv /var/www/repo/vendor/* && /bin/rm -f /var/www/repo/composer.lock && /usr/bin/curl -s http://getcomposer.org/installer | /usr/bin/php && cd /var/www/repo && /usr/bin/php /var/www/repo/composer.phar install',
		require =>  [Package['curl'], Package['git-core']],
			creates => "/var/www/repo/composer.lock"
				timeout => 0
}
- MANAGE USERS AND GROUPS
	1. CREATING GROUPS
	2. CREATING USERS
	3. UPDATING THE SUDOERS FILE
1. CREATING GROUPS
- group { "wheel":
	ensure => "present",
}
2. CREATING USERS
- user { "developer":
	ensure => "present",
	gid => "wheel",
	shell => "/bin/bash",
	home => "/home/developer",
	managehome => true,
	password => "passwordtest"
	require => Group["wheel"]
}
3. UPDATING THE SUDOERS FILE
- We can't define a user or group as having elevated privileges by using the user and group resource types
- We can use the EXEC command to push some text to the end of a sudoers file
- exec { "/bin/echo \"%wheel ALL=(ALL) ALL\" >> /etc/sudoers":
	require => Group["wheel"]
}
- SUBSCRIBE AND REFRESH ONLY
- Sometime we want to have a Puppet command run multiple times when other commands have finished. One example is restarting puppet
- We would want to do this:
	- When we import a new configuration file
	- When we install Apache modules such as PHP support and mod_rewrite
	- If we add new virtual hosts
- The SUBSCRIBE parameter instructs the command to run every time any of the commands in the subscribe option have been run 
- The REFRESHONLY parameter, when set to true, instructs the command to only run when one of the commands it subscribes to, has run
- exec { "reload-apache2":
	command => "/etc/init.d/apache2 reload",
	refreshonly => true,
	subscribe => File['/etc/apache2/sites-available/default'],
}
- PUPPET MODULES
- There are many existing, well written, re-usable Puppet modules freely available to use
	- http://forge.puppetlabs.com/

4. PROVISIONING WITH CHEF

- Chef is another provisioning tool which makes it easy for us to take a base OS install and turn it into a full-fledged server suited to the needs
- The configuration which determines how the server needs to be setup can be stored within our Vagrant project and can be sahred among teammates
- Info about how the server should be configured -> Chef recipes (written in Ruby
- We will use Chef Solo (standalone mode)
- Everything runs from one machine
- Chef also has server-client capabilities where you can define coockbooks and roles for all the servers in your infrastructure on a central host
- Chef is also idempotent
- CREATING COOCKBOOKS AND RECIPES WITH CHEF
- Chef instructions are recipes, which are bundled together in coockbooks
- Cookbooks can contain multiple recipes and other resources such as templates and files
- A cookbook is like a folder , with at least one recipe
- Default recipe file is default.rb
- Puppet & Chef both use Ruby
- Puppet is a Domain Specific Language based on Ruby
- Chef uses Ruby itself
- RESOURCES
- Chef uses resources to define actions and operations
- Resources are mapped to a Chef code, which varies depending on OS used
- Ubuntu mapped to apt-get, Fedora mapped to yum
- Resource types available:
	- Cron
	- Execute
	- File
	- Group
	- Package
	- Service
	- Template
	- User
- INSTALLING SOFTWARE
- Let's say we want to install Apache on our server
- Proceed the following process:
	1. Update our package manager
	2. Install the Apache package
	3. Run the Apache service
1. UPDATING OUR PACKAGE MANAGER
- execute "update-package-manager" do
	command "apt-get update"
	ignore_failure true
	timeout 6000
  end
- timeout 6000 overwrites the default of 3600 seconds 
- INSTALLING THE APACHE PACKAGE
- package "apache2" do
	action :install
  end
- RUNNING THE APACHE SERVICE
- service "apache2" do
	supports :status => true, :restart => true, :reload => true
		action [ :enable, :start]
  end
- UNDERSTANDING FILE MANAGEMENT
- We can:
	1. Copy Files
	2. Create symlinks
	3. Create folders
	4. Create multiple folders in one go
1. COPY FILES
- cookbook_file "/etc/apache2/sites-available/default" do
	backup false
	action :create_if_missing
  end
2. CREATE SYMLINKS
- link "/var/www/public" do
	to "/vagrant/src/public"
  end
3. CREATE FOLDERS
- directory "/var/www/uploads" do 
	owner "root"
	group "root"
	mode 00777
	action :create
  end
4. CREATE MULTIPLE FOLDERS IN ONE GO
- %w{dir1 dir2 dir3}.each do |dir|
	directory "/tmp/mydirs/#{dir}" do
	mode 00777
	owner "www-data"
	group "www-data"
	action :create
	end
  end
- MANAGING CRON
- cron "web_cron" do
	action :create
	command "/usr/bin/php /vagrant/cron.php"
	user "root"
	hour "1-4"
	minute "0,30"
  end
- RUNNING COMMANDS
- execute "compose" do
	command "/bin/rm -rfv /var/www/repo/vendor/* && /bin/rm -f /var/www/repo/composer.lock && /usr/bin/curl -s http://getcomposer.org/installer | /usr/bin/php && cd /var/www/repo && /usr/bin/php /var/www/repo/composer.phar install"
	creates "/var/www/repo/composer.lock"
	timeout 6000
  end
- MANAGING USERS AND GROUPS
- We can: 
	1. Create groups
	2. Create users
	3. Update the sudoers file
1. CREATE GROUPS
- group "wheel" do
	action:create
  end
2. CREATE USERS
- user "developer" do
	action :create
	gid "wheel" 
	shell "/bin/bash"
	home "/home/developer"
	supports {:manage_home => true}
	password "passwordtest"
  end
3. UPDATE THE SUDOERS FILE
- /bin/echo \"%wheel ALL=(ALL) ALL\" >> /etc/sudoers
- KNOWING COMMON RESOURCE FUNCTIONALITIES
- There is also a set of common functionality available to all resources
	- The ability to do nothing with the :nothing action
	- Shared attributes available to all resources: ignore_failure, provider, retries, retry_delay, and supports
	- The not_if and only_if conditions to ensure actions only run when certain conditions are met
	- Notifications to instruct other resources that another action has completed
- USING CHEF COOKBOOKS
- There are many existing, well-written, re-usable chef cookbooks available:
	- http://community.opscode.com/cookbooks
- USING CHEF TO PROVISION SERVERS
- Chef can run in its own right
- Provided Chef is installed, you can use chef-solo command, passing it with the location of the configuration file to use and a JSON file which contains attributes we wish to use:
	- chef-solo -c /home/michael/chefconfig.rb -j /home/michael/attributes.json

5. Provisioning with Vagrant using Puppet and Chef

- PROVISIONING WITHIN VAGRANT
- Vagrant relies on base boxes for the guest virtual machine; these are specifically pre-configured VM images, which have certain SW packages pre-installed and pre-configured
- Puppet & Chef are two such pre-installed SW packages
- Vagrant has its own interface through to these packages from the host machine
- We can provide some configuration in our Vagrant file and Vagrant will pass this information to the relevant provisioners on the guest VM
- PROVISIONING WITH PUPPET ON VAGRANT
- Vagrant supports two methods of using Puppet:
	1. Puppet in standalone mode, by using the Puppet apply command on the VM
	2. Puppet in client/server mode, whereby the VM will be configured from a central server
1. USING PUPPET IN A STANDALONE MODE
- We simply tell Vagrant where we have to put our Puppet manifests and modules, and what manifest should be run
- In vagrant file: config.vm.provision :puppet
- Along with this configuration, we will need a Puppet manifest called default.pp in the manifests folder of our project root
- This will instruct Vagrant to run the Puppet provisioner either when the machine boots up or if we run the Vagrant provision command
- We can modify the main options by provisioning configuration options as opposed to just telling Vagrant to provision with Puppet.
- We can specify the options for the location of manifests (puppet.manifests_path)
- The name of the Puppet manifest to apply (puppet.manifests_file)
- The location of any Puppet modules which we may reference within our Puppet manifest (puppet.module_path)
- config.vm.provision :puppet do |puppet|
	puppet.manifests_path = "provision/manifests"
	puppet.manifests_file = "default.pp"
	puppet.module_path = "provision/modules"
  end
- It is important for us to have the ability to change the manifest file, as Vagrant supports a multi VM environment, where a single project can have a number of virtual machines
- With this setup, we would need to tell Vagrant which manfest file to use for each of the machines, so that a web server can be properly configured as a web server and a DB server as a DB server
2. USING PUPPET IN CLIENT/SERVER MODE
- The configuration required for this is minimal, we simply tell Vagrant the address of the Puppet server we are using, and the name of our node
- config.vm.provision :puppet_server do |puppet|
	puppet.puppet_server = "puppet.internal.michaelpeacock.co.uk"
	puppet.puppet_node = "vm.internal.michaelpeacock.co.uk"
  end
- PROVISIONING WITH CHEF ON VAGRANT
- Vagrant also supports two methods of using Chef:
	1. Chef solo
	2. Chef in client/server mode with Chef client
1. USING CHEF SOLO
- The simplest way to use this within our project is simply to provide a Chef run list to Vagrant, this tells Vagrant which cookbooks should be applied
- config.vm.provision :chef_solo do |chef|
	chef.add_recipe "php"
  end
- This takes the PHP cookbook from the default cookbooks folder and applies it to the VM
- Cookbooks are stored in the cookbooks folder within the project root
- The chef.cookbooks_path setting allows us to override the cookbooks folder location
- config.vm.provision :chef_solo do |chef|
	chef.cookbooks_path = "provision/cookbooks"
  end
- We can also use Chef Roles by providing:
	- The location of the roles folder
	- The roles we wish to add to the VM
- config.vm.provision :chef_solo do |chef|
	chef.roles_path = "provision/roles"
	chef.add_role("web")
  end
2. USING CHEF IN A CLIENT/SERVER MODE
- To use Chef Client, we will need to tell Vagrant where the Chef Server is located and provide the authorization ket which will be used to authenticate the VM with the Server
- config.vm.provision :chef_client do |chef|
	chef.chef_server_url = "http://chef.internal.michaelpeacock.co.uk:4000/"
	chef.validation_key_path = "key.pem"
  end
- OTHER BUILT-IN PROVISIONERS
- In addition to Puppet & Chef provisioning options with Vagrant, there are two other methods:
	- SSH: Invoking commands via the terminal of the VM automatically through Vagrant
	- Ansible: A tool similar to Puppet & Chef, which is configured through a series of YAML files to define how a system should be provisioned
- PROVISIONING WITH SSH
- Two ways to use SSH provisioning:
	- Path: a file to execute
	- Inline: providing specific commands to run
- Both of these are shown as follows:
	- config.vm.provision :shell, :path => "provision/setup.sh"
	- config.vm.provision :shell, :inline => "apt-get install apache2"
- ANSIBLE PLAYBOOKS
- In order to use Ansible within Vagrant project, we need to tell Vagrant where the playbook and inventory files are
- config.vm.provision :ansible do |ansible|
	ansible.playbook = "provision/playbook.yml"
	ansible/inventory_file = "provision/hosts"
  end
- USING MULTIPLE PROVISIONERS ON A SINGLE PROJECT
- We can use multiple provisioners within a single project
- We simply need to put them in the order we wish for them to be executed
- vagrant.configure("2") do |config|
	config.vm.box = "precise64"
	config.vm.provision :shell, :inline => "apt-get-update"
	config.vm.provision :puppet do |puppet|
		puppet.manifests_path = "provision/manifests"
		puppet.manifest_file = "default.pp"
		puppet.module_path = "provision/modules"
	end
  end
- OVERRIDING PROVISIONING VIA THE COMMAND LINE
- There may be instances where we want to restrict or enforce the execution of provisioning or even a specific provisioner within a project
- The following commands are all executed from the host machine:
	- We can cancel a running provision by pressing CMD+C at the terminal
	- We can instruct Vagrant to re-run provisioning on a VM by using vagrant provision command
	- We can also add no-provision to the up and reload commands to instruct Vagrant to not run the provisioning tools when performing the up and reload actions
	- We can also provision with hust a specific provisioner should we wish, we would run vagrant provision provision-with puppet
 
