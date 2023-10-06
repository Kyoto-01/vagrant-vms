Vagrant.configure("2") do |config|

    config.vm.box = "debian/bullseye64"

	config.vm.hostname = "deb-dock-2"

	config.vm.network "public_network"

	config.vm.provider "virtualbox" do |vb|

		vb.name = "deb-dock-2"
		vb.memory = "1024"
		vb.cpus = "2"
		vb.customize ["modifyvm", :id, "--usbohci", "on"]

	end

	config.vm.provision "shell", inline: <<-SHELL

		# Update the apt package index and install packages to allow apt to use a repository over HTTPS and git

		apt update --fix-missing

		apt install -y \
			ca-certificates \
			curl \
			gnupg \
			git \
			vim

		# Add Docker’s official GPG key

		sudo install -m 0755 -d /etc/apt/keyrings

		curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

		sudo chmod a+r /etc/apt/keyrings/docker.gpg

		# Add docker repository

		echo "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] \
			https://download.docker.com/linux/debian "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  		tee /etc/apt/sources.list.d/docker.list > /dev/null

		apt update && apt install -y \
			docker-ce \
			docker-ce-cli \
			containerd.io \
			docker-buildx-plugin \
			docker-compose-plugin

	SHELL

	config.vm.provision "shell", inline: <<-SHELL

		apt update

    	apt install -y openssh-server

    	sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config

    	service ssh restart

    	useradd -m -s /bin/bash vagrant
		
    	echo 'vagrant:vagrant' | chpasswd

	SHELL

end
  