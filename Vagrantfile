# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

	# Based on Ubuntu Bionic 18.04; this is the default VM
	# run `vagrant up bionic`
	config.vm.define "bionic", primary: true do |bionic|
		bionic.vm.box = "ubuntu/bionic64"
		bionic.vm.synced_folder ".", "/pynq", 
			owner: "vagrant", group: "vagrant"
		bionic.vm.provider "virtualbox" do |vb|
			vb.gui = true
			vb.name = "pynq_ubuntu_18_04"
			vb.memory = "16384"
			vb.customize ["modifyvm", :id, "--cpus", 4]
			vb.customize ["modifyvm", :id, "--vram", "128"]
			vb.customize ['modifyvm', :id, '--graphicscontroller', 'vmsvga']
			vb.customize ["modifyvm", :id, "--accelerate3d", "on"]
			disk_image = File.join(File.dirname(File.expand_path(__FILE__)), 
				'ubuntu_18_04.vdi')
			unless File.exist?(disk_image)
			vb.customize ['createhd', 
						'--filename', disk_image, 
						'--size', 180 * 1024]
			end
			vb.customize ['storageattach', :id, 
						  '--storagectl', 'SCSI', 
						  '--port', 2, '--device', 0, 
						  '--type', 'hdd', 
						  '--medium', disk_image]
			vb.customize ['storageattach', :id, 
						  '--storagectl', 'IDE', 
						  '--port', '0', '--device', '1', 
						  '--type', 'dvddrive', 
						  '--medium', 'emptydrive']
		end

		bionic.vm.provision "shell", inline: <<-SHELL
			parted /dev/sdc mklabel msdos
			parted /dev/sdc mkpart primary 100 100%
			partprobe
			mkfs.xfs /dev/sdc1
			mkdir /workspace
			echo `blkid /dev/sdc1 | awk '{print$2}' | sed -e 's/"//g'` \
				/workspace   xfs   noatime,nobarrier   0   0 >> /etc/fstab
			mount /workspace
			chown -R vagrant:vagrant /workspace
			chmod 777 -R /workspace
		SHELL

		bionic.vm.provision "shell",
			inline: "apt-get update"

		bionic.vm.provision "shell", 
			inline: "/bin/bash /pynq/sdbuild/scripts/setup_host.sh"

		bionic.vm.provision "shell", 
			inline: "apt-get install -y --force-yes ubuntu-desktop"

		bionic.vm.provision "shell", 
			inline: "sudo fallocate -l 4G /swapfile"

		bionic.vm.provision "shell", 
			inline: "sudo chmod 600 /swapfile"

		bionic.vm.provision "shell", 
			inline: "sudo mkswap /swapfile"

		bionic.vm.provision "shell", 
			inline: "sudo swapon /swapfile"

		bionic.vm.provision "shell", 
			inline: "sudo echo \"/swapfile swap swap defaults 0 0\" | sudo tee -a /etc/fstab"

		bionic.vm.provision "shell", inline: <<-SHELL
			cat /root/.profile | grep PATH >> /home/vagrant/.profile
		SHELL
	end

end
