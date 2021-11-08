# Q1 - Did the assignment individually

# Q2 - The steps followed to compelete the assignment

* Configured the Ubuntu VM in VMware Workstation 16.

* Forked the Linux repository to the github account. 

*  sudo apt install git

* Cloned the repo using git clone command.

* Followed the following commands 

	1. cd linux 
	2. sudo apt-get install manpages-dev

* Edited the 283-1.c flies according to needs.

* Copied the config file using cp /boot/config-5.11.0-38-generic .config

* Had a swp file error resolved it by removing the swp file.


* Followed the following commands 

	1. make oldconfig 
	2. make prepare
	3. make clean 
	4. make prepare
	5. make -j 2 module
	6. make -j 2
	7. sudo make INSTALL_MOD_STRIP=1 modules_install
	8. sudo make install
	9. uname -a
	10. sudo reboot
	11. uname -a
	12. make
	13. sudo insmod cmpe283-1.ko
	14. lsmod | grep cmpe283
	15. demsg

* Got 0x0 register error, enebled nested VM in VMWare and changing few settings on my HOST OS (windows)

* Followed the following commands

	1. sudo rmmod cmpe283-1
	2. make
	3. sudo insmod cmpe283-1.ko
	4. demsg





