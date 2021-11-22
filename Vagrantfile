Vagrant.configure("2") do |config|
	config.vm.define "master" do |master|
	master.vm.box="centos/7"
		master.vm.provider :virtualbox do |v|
		v.customize ["modifyvm", :id, "--memory", 4096]
		v.cpus=2
	  end	 
	  master.vm.network "private_network",
		type:"dhcp"
	 master.vm.provision "shell", inline: <<-SHELL 
	 sudo yum install wget -y
	 sudo yum install vim -y
	 sudo yum install docker -y
	 SHELL
	end

	config.vm.define "minion" do |minion|
		minion.vm.box="centos/7"
		minion.vm.provider :virtualbox do |v|
			v.customize ["modifyvm", :id, "--memory", 4096]
			v.cpus=2
			end
		minion.vm.network "private_network",
			type:"dhcp"
		minion.vm.provision "shell", inline: <<-SHELL 
		sudo yum install wget -y
		sudo yum install vim -y
		sudo yum install docker -y
		SHELL
	end
end