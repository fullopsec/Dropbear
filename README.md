 # Dropbear SSH

This code is a set of commands that guides the user on how to install and configure the Dropbear SSH server on an Ubuntu 22.04 machine. The purpose of Dropbear is to allow remote access to the machine during the boot process in case of disk encryption. By installing and configuring Dropbear on the machine, users can unlock encrypted disks remotely using SSH.



## Video tutorial

https://www.youtube.com/watch?v=7TLPExkUHqw

## Update your machine

	sudo apt update
	sudo apt upgrade
## Install Dropbear
	sudo apt install dropbear-initramfs
## Configure Dropbear
	sudo -i
	cd /etc/dropbear/initramfs/
	
### Configure dropbear.conf :

Options:

- -I : Disconnect the session if no traffic is transmitted or received in x seconds

- -j: Disable ssh local port forwarding.

- -k : Disable remote port forwarding.

- -p : Dropbear listen on this specified TCP port.

- -s : Disable password logins.

Example:

	DROPBEAR_OPTIONS="-I 239 -j -k -p 5768 -s"
	
### Edit /etc/initramfs-tools/initramfs.conf :

Format:

	IP=SERVER_IP::ROUTER_IP:NETMASK:SERVER_HOSTNAME
Example:

	IP=192.168.1.36::192.168.1.1:255.255.255.0:node2
	
## Update initramfs
	sudo update-initramfs -u -v
	
## Create the keys

Generate a new key on the client:

	ssh-keygen -t rsa -f ~/.ssh/dropbear 					#(command to do on the unlocking machine)

Copy the key to the server:

	scp ~/.ssh/dropbear.pub daniel@192.168.1.36:~/dropbear.pub  		#(command to do on the unlocking machine)
	
Add the key to the initramfs authorized keys:

	cat /home/daniel/dropbear.pub >> /etc/dropbear/initramfs/authorized_keys

Stop being root

	exit   
	
Update initramfs again

	sudo update-initramfs -u
	
## Making an alias (optional)

Edit the bashrc on the client that will remotely unlock the server

	nano ~/.bashrc  
	
Add the alias:

Format:

	alias <Aliasname>="ssh -i ~/.ssh/dropbear -p <port> -o 'HostKeyAlgorithms ssh-rsa' root@<SERVER_IP> 'echo -n <DRIVE_ENCRYPTION_PASSWORD> | cryptroot-unlock'"
	
Exemple:

	alias node2unlock="ssh -i ~/.ssh/dropbear -p 5768 -o 'HostKeyAlgorithms ssh-rsa' root@192.168.1.36 'echo -n test | cryptroot-unlock'"  
	
Source the bashrc:	

	source .bashrc 		#(on the unlocking machine)

Reboot:

	sudo reboot now
	
Try to unlock the server with your alias:

	node2unlock 		

## manual unlock with no alias:

	ssh -i ~/.ssh/dropbear -p <port> -o "HostKeyAlgorithms ssh-rsa" root@<SERVER_IP>
	cryptroot-unlock
