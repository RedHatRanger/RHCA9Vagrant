### Quiz 1: Resolving Boot Loader Issues on UEFI Systems

#### Questions:
1. On which directory does the Anaconda installer mount the UEFI partition? \
   a. /boot/grub2  
   b. /boot/efi  
   c. /etc/default  
   d. /etc/grub.d/

2. Which directory stores the GRUB2 configuration files and the UEFI firmware applications? \
   a. /boot/grub2  
   b. /etc/default  
   c. /boot/efi/EFI/redhat  
   d. /boot/loader  

3. Which packages are needed to reinstall deleted files from /boot/efi on UEFI-based systems? \
   a. grub2-efi and shim  
   b. grub2-mkconfig and grub2-efi  
   c. shim and grub2-install  
   d. grub2-efi and grub2-install  

4. Which command sets the default kernel? \
   a. grub2-mkconfig --default  
   b. kernel-install add  
   c. grub2-install  
   d. grubby --set-default

(scroll down for the answers)
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>


#### Answers:
1. On which directory does the Anaconda installer mount the UEFI partition?
   - [ ] a. /boot/grub2  
   - [x] b. /boot/efi
   - [ ] c. /etc/default  
   - [ ] d. /etc/grub.d/

2. Which directory stores the GRUB2 configuration files and the UEFI firmware applications?
   - [ ] a. /boot/grub2  
   - [ ] b. /etc/default  
   - [x] c. /boot/efi/EFI/redhat
   - [ ] d. /boot/loader  

3. Which packages are needed to reinstall deleted files from /boot/efi on UEFI-based systems?
   - [x] a. grub2-efi and shim*  
   - [ ] b. grub2-mkconfig and grub2-efi  
   - [ ] c. shim and grub2-install  
   - [ ] d. grub2-efi and grub2-install  

4. Which command sets the default kernel?
   - [ ] a. grub2-mkconfig --default  
   - [ ] b. kernel-install add  
   - [ ] c. grub2-install  
   - [x] d. grubby --set-default

