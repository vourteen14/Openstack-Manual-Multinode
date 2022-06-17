PRE-OS-SETUP
 - A running operating system ubuntu
 - Network has configured
 - Minimum disk 40 G
 - Minumum memory 4G
 - Minimum processor 1 core
 - Openstack version in Wallaby

Sistem Information
 - Hostname: control
 - OS: Ubuntu 20.04
 - Memory: 4G
 - Processor: 2 core
 - Harddisk: 40G
 - Network: 30.30.30.251

SETUP Operating System
- Add user to sudoer without password ( in case username is anggasuriana )
 - sudo su >> enter password
 - echo "anggasuriana ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/angga
 - exit && sudo su ( if no ask password adding success )

- Update and Upgrade Dependency
 - sudo apt update && sudo apt upgrade -y

SETUP Pre Requirement For Openstack
- Add repository requirement for Openstack
 - sudo apt install software-properties-common -y
 - sudo add-apt-repository cloud-archive:wallaby
 - sudo apt update && sudo apt upgrade -y

DONE FOR PRE-REQUIREMENT
