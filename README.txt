Install Dropbear on Ubuntu 22.04:

video : https://www.youtube.com/watch?v=7TLPExkUHqw

commands used:
sudo apt update
sudo apt upgrade
sudo apt install dropbear-initramfs
sudo -i
cd /etc/dropbear/initramfs/
nano dropbear.conf
	DROPBEAR_OPTIONS="-I 239 -j -k -p 5768 -s"
nano /etc/initramfs-tools/initramfs.conf
	IP=192.168.1.36::192.168.1.1:255.255.255.0:node2
sudo update-initramfs -u -v
ssh-keygen -t rsa -f ~/.ssh/dropbear 					#(on the unlocking mackine side)
cat ~/.ssh/dropbear.pub							#(on the unlocking mackine side)
scp ~/.ssh/dropbear.pub daniel@192.168.1.36:~/dropbear.pub  		#(on the unlocking mackine side)
cat /home/daniel/dropbear.pub >> /etc/dropbear/initramfs/authorized_keys
exit   (the root mode)
sudo update-initramfs -u
nano .bashrc  		#(on the unlocking mackine side)
	alias node2unlock="ssh -i ~/.ssh/dropbear -p 5768 -o 'HostKeyAlgorithms ssh-rsa' root@192.168.1.36 'echo -n test | cryptroot-unlock'"   #(on the unlocking mackine side)
source .bashrc 		#(on the unlocking mackine side)
sudo reboot now
node2unlock 		#(on the unlocking mackine side)

manual unlock no alias:
ssh -i ~/.ssh/dropbear -p 5768 -o "HostKeyAlgorithms ssh-rsa" root@192.168.1.36
cryptroot-unlock
