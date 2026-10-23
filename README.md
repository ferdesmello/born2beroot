_This project has been created as part of the 42 curriculum by ferde-so._

# born2beroot

## Description

The goal of this 42 school's project is to create a virtual machine using VirtualBox with strict specifications.

section that clearly presents the project, including its goal and a brief overview.

## Instructions

section containing any relevant information about compilation, installation, and/or execution.

## Resources

section listing classic references related to the topic (documentation, articles, tutorials, etc.)

### AI use

a description of how AI was used — specifying for which tasks and which parts of the project.

## Project description 

I decided to use Debian because I already have some (limites) experience using Ubuntu / Debian systems and they are usually more simple to use and begginers friendly than 
choice of operating system (Debian or Rocky) including their respective advantages and disadvantages.

main design choices made during the setup 

(partitioning, 

security policies, 

user management, 

services installed) 



* Debian vs Rocky Linux

Both are operational systems (OS) build around the Linux kernel, but they differ in the name and functionality of many equivalent systems.

Debian is a free, widely popular, and general porpose OS with a very big repository of free software to install from. It is flexible and more suitable for begginers and hobbists.

Rocky Linux is free clone OS of the comertial RHEL (Red Hat Enterprise Linux) produced by Red Het / IBM. It may be more suitable for people interested in enterprise compatibility versions and long life span support per version.

* AppArmor vs SELinux

Both are security systems developed to provide Mandatory Access Control (MAC) for Linux, also restricting what programs can do, even if they have root privileges, and both use the Linux Security Modules (LSM) system to enforce rules.

AppArmor is the default on Debian, Ubuntu, etc. It has file restrictions based on path, with applications restrictions applyed to profiles, and is easy and simple to use for begginers.

SELinux is the default on RHEL, Rocky Linus, etc. It has file restrictions based on label, so it requires support for extended attributes, with restrictions affection the entire system, and it has a complex and advanced interface.

* UFW vs firewalld

Uncomplicated Firewall (UFW) and Firewall Daemon (firewalld) are tools to facilitate the administrattion of Linux native real firewall.

While UFW is the default on Debian, Ubuntu, etc, firewalld is the default on RHEL, Rocky Linus, etc. Their main differences are in dynamic management of changes in the rules, the concept security zones, and the complexity of use.

UFW requires reboot of the firewall when rules are changed, has global rules applyed to doors, and is easy and simple to use.

Firewalld dynamically changes the rules without loss of connection, can attribute networks to different groups of security, and is more complex and advanced to use.

* VirtualBox vs UTM

VirtualBox and Universal Turing Machine (UTM) are both tools for the creation of virtual machines.

VirtualBox is focused on Intel and AMD architectures, virtualizing only machines of the same CPU architecture, having only very limited support for Apple's M series.

UTM is focused on Apple's M series architectures but it is also more versatile as it can also emulate machines made for Intel and AMD architectures.