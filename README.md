# CMPE283 Assignment 1

Completed as individual

## Step-by-Step Instructions
- Configure Xubuntu 20.04.3 64-Bit on VMware Workstation (4 Processors, 8 GB Memory, 50 GB Hard Disk, Nested Virtualization Enabled)
- Install all necessary requirements in the terminal using the following commands:
  - `sudo apt install git`
  - `sudo apt install build-essential`
  - `sudo apt install flex bison`
  - `sudo apt install libssl-dev`
  - `sudo apt install libelf-dev`
  - `sudo apt install dwarves`
- Fork the Linux GitHub repository and then clone your forked repository into your VM's home directory
- Run the following commands to begin the process of building the Linux Kernel
  - `cd linux`
  - Create a folder, cmpe283, and place Makefile and cmpe283-1.c files provided into this folder within the linux directory
  - Modify cmpe283-1.c in order to obtain required output
    - Additionally, add `MODULE_LICENSE("GPL v2");` to the bottom of cmpe283-1.c to avoid license error
  - `uname -a`
    - Displays the config file that you want to copy, in my case it was 5.11.0-40-generic
  - `cp /boot/config-5.11.0-40-generic .config`
    - If you run into certificate issues, modify the .config file
      - Set the lines, `CONFIG_SYSTEM_TRUSTED_KEYS` and `CONFIG_SYSTEM_REVOCATION_KEYS` to empty strings
  - `make oldconfig`
    - Might get asked a bunch of questions, just press and hold enter to give default answers until all questions are answered
  - `make prepare`
  - `make -j 4 modules`
    - The number "4" represents the number of processors assigned to your VM and the number of Make jobs running at once
  - `make -j 4`
  - `sudo make INSTALL_MOD_STRIP=1 modules_install`
  - `sudo make install`
  - `sudo reboot`
    - This will reboot the VM
  - `uname -a`
    - Now displays the new Linux kernel from GitHub, which as of this writing is 5.15.0+, instead of 5.11.0-40-generic from earlier
  - `cd linux/cmpe283`
  - `make`
  - `sudo insmod cmpe283-1.ko`
    - Insert the module into the kernel
  - `dmesg`
    - ![Capture](https://user-images.githubusercontent.com/2999334/141731903-8fba6a29-2c21-4ad4-a67e-fb0fb507b922.PNG)
  - `sudo rmmod cmpe283-1`
    - Remove the module out of the kernel
  - `dmesg`
    - ![Capture2](https://user-images.githubusercontent.com/2999334/141731919-a5066e6f-4096-4296-8ca7-f13483642a46.PNG)
  - Commit any changes to your forked repository on GitHub





# CMPE283 Assignment 2

Group Members: Martin Duong & Ruchit Patel

Work Distribution:
- **Martin Duong**: Implemented 0x4FFFFFFF according to professor's video with slight modifications to get kernel to build. Handled the building of the kernel, setting up the VM environments, and testing the program.
- **Ruchit Patel**: Implemented 0x4FFFFFFE. Added variables in cpuid.c to calculate the processing time of exits. Modified kvm_emulate_cpuid to fetch the processing time when eax=0x4FFFFFFE. Displayed the high 32 bits of the total time spent processing all exits in %ebx and low 32 bits of the total time spent processing all exits in %ecx. Coded the vmx_handle_exit to calculate processing time of exits in vmx.c.

## Step-by-Step Instructions
Assumption: Running on a VM with Xubuntu 20.04 with environment setup from Assignment 1

Note: In our case, following the professor's video exactly for 0x4FFFFFFF resulted in error which was solved by moving around the variable declarations and using `EXPORT_SYMBOL`

1. Implement CPUID leaf nodes: 0x4FFFFFFF and 0x4FFFFFFE in cpuid.c and vmx.c within the Linux kernel
2. Change your directory to where you placed the Linux kernel
3. `make -j 4 modules`
4. `sudo bash`
5. `make MOD_INSTALL_STRIP=1 modules_install`
	- We don't have to run this command with the additional "make install" since we haven't changed the version of the kernel we're running
6. Make sure kvm module for hypervisor is not already loaded
	- `lsmod | grep kvm`
	- If it is then:
		- `rmmod kvm`
		- `rmmod kvm_intel`
			- Run this first if you are presented with `ERROR: Module kvm is in use by kvm_intel`
	- If nothing is returned then kvm hypervisor is not running, next:
		- `modprobe kvm`
			- If you get missing symbol error then change around where variables are declared in cpuid.c and vmx.c
		- `lsmod | grep kvm`
			- Check if kvm is loaded
		- `modprobe kvm_intel`
		- `lsmod | grep kvm_intel`
		  - This should ensure that kvm and kvm_intel are loaded properly and you should get the result below
		  - ![Capture](https://user-images.githubusercontent.com/2999334/142976420-d320ea22-9eed-4eba-bb1a-e3fcb6ac5bdb.PNG)
		
7. Install kvm on VM running Xubuntu 20.04
8. Create a VM with kvm running Lubuntu 21.10, 2GB Memory, 1 Processor
9. Boot into VM running Lubuntu 21.10 (VM within a VM)
10. Install CPUID package on inner VM
11. On the terminal, run `cpuid -l [Insert leaf node here]` (Example: `cpuid -l 0x4FFFFFFF`)
12. Here is our environment showing the host VM and the inner VM
  - Running `sudo virsh -c qemu:///system list` in our host VM reveals the inner VM
  - ![Capture2](https://user-images.githubusercontent.com/2999334/142979558-9f4fe05a-7f6b-4456-8dfd-efb1ed3178e7.PNG)
13. Here are our results from the inner VM after running `cpuid -l 0x4FFFFFFF` and `cpuid -l 0x4FFFFFFE`
  - ![Capture3](https://user-images.githubusercontent.com/2999334/142979577-b62533ea-0b3c-47b4-a77c-b6feb25c48c8.PNG)
14. Here are our results from the host VM running `dmesg`
  - ![Capture4](https://user-images.githubusercontent.com/2999334/142979587-bc591503-d353-4deb-933b-fe096f4a8900.PNG)





# CMPE283 Assignment 3

Group Members: Martin Duong & Ruchit Patel

Work Distribution:
- **Martin Duong**: Implemented 0x4FFFFFFC. Handled the building of the kernel, setting up the VM environments, and testing the program.
- **Ruchit Patel**: Implemented 0x4FFFFFFD. Added variables in cpuid.c to calculate the number of individual exits. Modified the kvm_emulate_cpuid, so that can fetch the number of exits when eax=0x4FFFFFFD and ecx = id of exit. Display the number of exits due to that individual exit reason in eax and then print eax. Coded the vmx_handle_exit to calculate number of exits of particular type in vmx.c.

## Step-by-Step Instructions
Assignment 3 is a continuation of Assignment 2, so the same steps can be followed, just changing what CPUID leaf node is chosen within the inner VM.

1. Implement CPUID leaf nodes: 0x4FFFFFFC and 0x4FFFFFFD in cpuid.c and vmx.c within the Linux kernel
2. In the case of these two leaf nodes, to return the number of exits or CPU cycles for a specific exit reason, do the following command `cpuid -l 0x4FFFFFF[C/D] -s [Exit Reason]`, `-l` specifies the leaf and `-s` specifies the subleaf (Example: `cpuid -l 0x4FFFFFFC -s 0` to get the number of CPU cycles on exit reason 0)
3. Here are our results from the inner VM after running `cpuid -l 0x4FFFFFFC` and `cpuid -l 0x4FFFFFFD`
  - ![Capture4](https://user-images.githubusercontent.com/2999334/143945562-d57879c6-a825-44ae-a83d-d58fe19b3209.PNG)
4. Here are our results from the host VM running `dmesg`
  - ![Capture2](https://user-images.githubusercontent.com/2999334/143945579-06246f78-4bb7-446f-a930-7d14c48af688.PNG)
  - ![Capture3](https://user-images.githubusercontent.com/2999334/143945587-32b2f949-fade-42d8-a3ac-ef337ef74f22.PNG)

## Questions
1. Comment on the frequency of exits – does the number of exits increase at a stable rate? Or are there
more exits performed during certain VM operations? Approximately how many exits does a full VM
boot entail?
  - Most exits seem to never occur as they stay 0 even after multiple runs of CPUID. While doing nothing in the inner VM, it looks like HLT and external interrupts increase at a stable rate and CPUID exits increase as we call CPUID. When doing IO, IO instruction exits increase signifcantly as expected. There are several exits, MOV DR, PAUSE, WBINVD or WBNOINVD, and XSETBV, that occur during VM boot but remain stagnant afterwards.
  - Starting up the inner VM and running `cpuid -l 0x4FFFFFFF` returns 6491169 exits, and then performing a reboot of the inner VM and running `cpuid -l 0x4FFFFFFF` again returns 7490187 exits, for a difference of 999,018 exits. Therefore, a full VM boot gives us 6491169 exits while rebooting the VM gives us approximately 1 million exits per reboot.
  - ![Capture](https://user-images.githubusercontent.com/2999334/143944560-693f2e1c-1c2a-42d4-9ad0-f847ddf82fb1.PNG)

2. Of the exit types defined in the SDM, which are the most frequent? Least?
  - The most frequent exits (100000+ exits) are: External interrupt, CPUID, HLT, Control-register accesses, I/O instruction, WRMSR, EPT violation, and EPT misconfiguration.
  - The least frequent exits (1-1000 exits) are: MOV DR, PAUSE, Access to GDTR or IDTR, WBINVD or WBNOINVD, and XSETBV.

# CMPE 283 Assignment 4

Group Members: Martin Duong & Ruchit Patel

Martin Duong: Ran assignment with/without EPT

Ruchit Patel: Answered questions

Assumptions: Working Assignment 3

## Step-by-Step Instructions
![Steps](https://user-images.githubusercontent.com/2999334/144771357-65f95d75-b238-43f0-afa0-cf3a82c355d5.PNG)

## With EPT (Nested Paging)
![NestedPaging](https://user-images.githubusercontent.com/2999334/144771253-2d5d55ef-13d9-41e2-a5b1-e193df2308d0.PNG)

## Without EPT (Shadow Paging)
![ShadowPaging](https://user-images.githubusercontent.com/2999334/144771256-b29a9054-bcc1-4eba-95cc-a8490af4a522.PNG)

## Questions
1) What did you learn from the count of exits? Was the count what you expected? If not, why not?
  - We observed that the count of exits greatly increased without EPT (shadow paging). The count was what we expected since with shadow paging, the VMM needs to intervene and handle exits much more often as we need to turn on more exits, such as exit when there is a change in CR3 and exit on page fault. On the other hand, nested paging accesses the page table directly without the need to constantly exit.
2) What changed between the two runs (ept vs no-ept)?
  - Between the two runs, there were a couple of differences, first being that when booting up the VM without EPT (shadow paging), the bootup process was slower than with EPT (nested paging). Secondly, without EPT, we observed three exit reasons that were not observed with EPT; the exit reasons were 14 (INVLPG), 33 (VM-entry failure due to invalid guest state), and 58 (INVPCID).
