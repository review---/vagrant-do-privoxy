Vagrant.configure('2') do |config|
	config.vm.hostname							= 'do-proxy01'
	config.vm.synced_folder ".", "/vagrant", :disabled => true
#	config.ssh.port									= '443'
	config.vm.provider :digital_ocean do |provider, override|
		override.ssh.private_key_path = '~/.ssh/id_rsa'
		override.vm.box								= 'digital_ocean'
		override.vm.box_url						= "https://github.com/smdahlen/vagrant-digitalocean/raw/master/box/digital_ocean.box"
#		override.ssh.proxy_command = "/usr/local/bin/corkscrew <proxy_ip> <proxy_port> %h %p"
		provider.image								= 'centos-6-5-x64'
		provider.region								= 'sgp1'
		provider.token								= 'your token here'
		provider.size									= '512MB'
	end
	config.vm.provision "ansible" do |ansible|
		ansible.playbook = "proxy.yml"
		ansible.sudo = true
	end
end
