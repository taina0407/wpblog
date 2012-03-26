http://wiki.opscode.com/display/chef/Fast+Start+Guide

Install chef server

http://wiki.opscode.com/display/chef/Installing+Chef+Server+on+Debian+or+Ubuntu+using+Packages

	echo "deb http://apt.opscode.com/ `lsb_release -cs`-0.10 main" | sudo tee /etc/apt/sources.list.d/opscode.list
	sudo mkdir -p /etc/apt/trusted.gpg.d
	gpg --keyserver keys.gnupg.net --recv-keys 83EF826A
	gpg --export packages@opscode.com | sudo tee /etc/apt/trusted.gpg.d/opscode-keyring.gpg > /dev/null
	sudo apt-get update
	sudo apt-get install opscode-keyring
	sudo apt-get install chef chef-server

http://chefserver.eee168.com:4000
rabbitmq consumer: chef letoueee168
webui: admin letoueee169 -> letoueee168

# Configure the Command Line Client

	mkdir -p ~/.chef
	sudo cp /etc/chef/validation.pem /etc/chef/webui.pem ~/.chef
	sudo chown -R $USER ~/.chef

	chef-server > knife configure -i
	Where should I put the config file? [~/.chef/knife.rb] 
	Please enter the chef server URL: [http://localhost:4000] 
	Please enter a clientname for the new client: [ping]
	
	Please enter the existing admin clientname: [chef-webui] 
	Please enter the location of the existing admin client's private key: [/etc/chef/webui.pem] .chef/webui.pem

	Please enter the validation clientname: [chef-validator] 
	Please enter the location of the validation key: [/etc/chef/validation.pem] .chef/validation.pem

# create a client account

	knife client create pkufranky -n -a -f /tmp/pkufranky.pem
	knife client show pkufranky

# access webui

http://chefserver.eee168.com:4040

admin:letoueee169
改密码为letoueee168

"Tampered with cookie" message when opening the chef web UI
The chef web UI signs its cookies. When you create a completely new chef installation (e.g. by using this script), it will create a new session signing key. If you have visited a previous chef installation under the same URL (http://chef:4040/) before, your browser might still have cookies signed with the old key, which will lead to this message. As a workaround, delete those cookies from your browser cache.



# setup chef workstation at chefbox

http://wiki.opscode.com/display/chef/Workstation+Setup+for+Debian+and+Ubuntu

	sudo apt-get install ruby ruby-dev libopenssl-ruby rdoc ri irb build-essential wget ssl-cert curl

Install rubygems from source
http://rubygems.org/

	cd /tmp
	curl -O http://production.cf.rubygems.org/rubygems/rubygems-1.8.10.tgz
	tar zxf rubygems-1.8.10.tgz
	cd rubygems-1.8.10
	sudo ruby setup.rb --no-format-executable
	sudo gem update -V -V --system

	sudo gem install chef -V -V --no-ri --no-rdoc
	sudo apt-get -y install git-core

	cd ~
	git clone git://github.com/opscode/chef-repo.git
	mkdir -p ~/chef-repo/.chef
	rsync -avz chefserver:/tmp/pkufranky.pem ~/chef-repo/.chef/
	rsync -avz chefserver:.chef/validation.pem ~/chef-repo/.chef/
	cd ~/chef-repo
	knife configure
	knife client list

`knife configure`就是用来生成knife.rb, 编辑生成的`~/chef-repo/.chef/knife.rb`如下

	current_dir = File.dirname(__FILE__)
	log_level                :info
	log_location             STDOUT
	node_name                "pkufranky"
	client_key               "#{current_dir}/pkufranky.pem"
	validation_client_name   'chef-validator'
	validation_key           "#{current_dir}/validation.pem"
	chef_server_url          'http://chefserver.eee168.com:4000'
	cache_type               'BasicFile'
	cache_options( :path => "#{ENV['HOME']}/.chef/checksums" )
	cookbook_path            ["#{current_dir}/../cookbooks"]

查看目前为止的clients

	$ cd ~/chef-repo
	$ knife client list
	  chef-validator
	  chef-webui
	  chefserver.eee168.com
	  ping
	  pkufranky

prepare cookbook

	cd ~/chef-repo
	for cb in chef-client yum rabbitmq mongodb erlang apt
		do knife cookbook site install $cb
	done
	knife cookbook upload -a

http://wiki.opscode.com/display/chef/Client+Bootstrap+Fast+Start+Guide
on chefbox, bootstrap webdev (192.168.0.4)

	knife bootstrap 192.168.0.4 -N webdev -r 'recipe[chef-client]' -x ping --sudo
	knife client list # see webdev

Add new recipe to NODENAME

	knife node run_list add NODENAME 'recipe[getting-started]'


http://wiki.opscode.com/display/chef/Cookbook+Fast+Start+Guide

# data bag

	knife data bag create users
	knife data bag create apps
	knife data bag from file apps data_bags/apps/webapi.json

# 添加/删除 ssh key

data_bags/users/{www-data,himalayas}.json中添加/删除ssh key

	cd chef-repo
	knife data bag from file users data_bags/users/www-data.json
	knife data bag from file users data_bags/users/himalayas.json
