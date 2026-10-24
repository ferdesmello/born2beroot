_This project has been created as part of the 42 curriculum by ferde-so._

# Born2Beroot

## Description

Born2Beroot is a system administration project from the 42 curriculum. Its goal is to create and configure a secure virtual machine according to a strict set of requirements.

The project introduces virtualization, Linux system administration, disk encryption, user and group management, password policies, SSH, firewall configuration, sudo privileges, and system monitoring.

I implemented the project using Debian in VirtualBox, without a graphical desktop environment. The system is configured to use encrypted LVM partitions, a non-root administrative user, SSH on a non-default port, UFW, and a monitoring script that periodically broadcasts system information to logged-in terminals.

## Instructions

### Virtual machine

The virtual machine was created with VirtualBox and Debian. The virtual disk is dynamically allocated, with a maximum size of 30 GB.

The VM can be started by opening its `.vbox` configuration file in VirtualBox. During startup, the disk encryption passphrase must be entered to unlock the encrypted volumes.

No graphical desktop environment is installed. The system boots into the multi-user command-line target.

### Accessing the system

The configured hostname is `ferde-so42`. The regular user is `ferde-so`, who belongs to the `user42` and `sudo` groups.

SSH is configured to listen on port `4242`, and root login through SSH is disabled.

To connect from another machine, use:

```bash
ssh ferde-so@<VM_IP_ADDRESS> -p 4242
```

Replace `<VM_IP_ADDRESS>` with the VM's reachable IPv4 address. Network configuration must allow the connecting machine to reach the VM.

### Monitoring script

The monitoring script is located at `/usr/local/bin/monitoring.sh`. It displays system information such as the operating system and kernel, CPU, memory and disk usage, last boot time, LVM status, active TCP connections, logged-in users, network information, and sudo command count.

The script is scheduled through root's crontab to run at startup and every ten minutes. Its output is broadcast to terminals using `wall`.

To run it manually:

```bash
sudo /usr/local/bin/monitoring.sh
```

To inspect the root crontab:

```bash
sudo crontab -l
```

## Resources

- Born2Beroot subject
- [Debian](https://www.debian.org/)
- [Wikipedia](https://wikipedia.org/)
- [reddit](https://www.reddit.com/)
- [VirtualBox](https://www.virtualbox.org/)
- [Boorn2Beroot guide](https://noreply.gitbook.io/born2beroot)

### AI use

AI assistance was used to clarify Linux, virtualization, and shell concepts, explain configuration choices, troubleshoot installation and configuration issues, review commands and scripts, and to follow on the step by step of the configuration.

AI was also used to discuss the differences between Debian and Rocky Linux, AppArmor and SELinux, UFW and firewalld, and VirtualBox and UTM.

## Project description

### Choice of operating system: Debian

Debian was choosen because I already had some experience with Ubuntu and Debian-based systems. Their command-line tools and package-management approach were therefore more familiar to me, making Debian a practical choice for this project. Besides, it seemed much more easy to configure and to find guides online for the configuration.

### Main design choices

#### Partitioning and encryption

The virtual disk was partitioned and encrypted using the Logical Volume Manager (LVM). The encrypted container protects the data stored on the virtual disk when it is not unlocked. LVM provides logical volumes that can be managed separately within the volume group.

The logical volumes include the root filesystem (`/`), `/home`, `/var`, `/tmp`, and `swap`. Separating these areas provides more flexibility in allocating disk space and managing filesystem usage.

Disk encryption requires the passphrase during boot to unlock the encrypted volumes. It protects data at rest, but it does not replace account security, access controls, or other system protections.

#### User and group management

The regular account is `ferde-so`, and the hostname is `ferde-so42`, following the project's naming requirement.

The user belongs to the `user42` and `sudo` groups. Administrative tasks are performed through sudo rather than by routinely logging in as root.

#### Password policy

Password-aging settings are configured to require password expiration after 30 days, enforce a minimum of two days between password changes, and issue a warning seven days before expiration.

Password-quality settings enforce a minimum length of ten characters, require uppercase and lowercase letters and digits, limit repeated consecutive characters, prevent the username from being included in the password, and require a difference of at least seven characters from the previous password where applicable.

These policies are intended to encourage stronger passwords and reduce the risk of weak or frequently reused credentials.

#### SSH and firewall

The Secure Shell (SSH) service listens on Transmission Control Protocol (TCP) port `4242`, and direct root login through SSH is disabled.

Uncomplicated Firewall (UFW) is enabled as the firewall. Incoming connections are denied by default, outgoing connections are allowed by default, and TCP port `4242` is allowed for SSH access.

This configuration limits incoming network access to the service required by the project.

#### Sudo configuration

Sudo is configured to allow a maximum of three password attempts, display a custom message after an incorrect password, require a terminal, and log commands and input/output activity. The sudo configuration also restricts the executable search path through `secure_path`.

Sudo logs are configured under `/var/log/sudo/`.

#### Monitoring

The `monitoring.sh` script collects system information using standard Linux commands and utilities. Cron schedules it to run at startup and every ten minutes, while `wall` broadcasts the report to available terminals.

This provides a recurring summary of the system's state without requiring a graphical monitoring application.

## Comparisons

### Debian vs Rocky Linux

Both are Linux distributions that use the Linux kernel, but they differ in their package-management tools, release practices, default configurations, and ecosystem.

Debian is a free, general-purpose Linux distribution with a large software repository and a strong emphasis on stability. It uses APT (Advanced Package Tool) and `.deb` packages. It is a general-purpose distribution with a large repository and a long history of community development.

Rocky Linux is a free, community-supported enterprise Linux distribution designed to be compatible with RHEL (Red Hat Enterprise Linux). It is a useful choice for people interested in enterprise Linux environments and their administration conventions. It uses DNF (Dandified YUM) and RPM (RPM Package Manager) packages and aims for compatibility with RHEL. Its conventions and lifecycle make it particularly relevant to enterprise Linux administration.

### AppArmor vs SELinux

AppArmor and SELinux are Linux security systems that implement Mandatory Access Control (MAC). They can restrict what applications are allowed to access, even when an application runs with elevated privileges.

AppArmor (Application Armor) primarily uses file paths and application profiles to define restrictions. It is enabled on this Debian installation and provides a profile-based approach to restricting application behaviour.

SELinux (Security-Enhanced Linux) primarily uses security labels, types, and policies to control access. It is commonly associated with RHEL-compatible distributions, including Rocky Linux. Its policy model can be more complex to learn and administer.

### UFW vs firewalld

UFW (Uncomplicated Firewall) and firewalld (firewall daemon) are tools for managing Linux firewall rules.

UFW provides a relatively simple interface for defining incoming and outgoing rules. It is commonly used on Debian- and Ubuntu-based systems.

Firewalld supports zones, which associate different trust levels and rule sets with network interfaces or connections. It also supports runtime changes to firewall rules and is commonly used on RHEL-compatible systems.

Firewall rule changes do not inherently require a full system reboot in either tool. The main differences are their interfaces, configuration models, and management features.

### VirtualBox vs UTM

VirtualBox and UTM (Universal Turing Machine) are applications used to run virtual machines, but they differ in their platform support and virtualization approaches.

VirtualBox is a general-purpose virtualization application available on several desktop operating systems. Its virtualization capabilities depend on the host architecture and the guest operating system being used.

UTM is particularly associated with macOS and Apple Silicon. It uses Apple's virtualization framework where supported and emulation, including emulating a different CPU architecture when required.

The choice depends on the host computer, guest architecture, operating-system support, and the performance required.

## Verification commands

The following commands were useful for checking the system configuration.

#### No graphical interface

* ulti-user.target / graphical.target

```bash
systemctl get-default
```

* No graphycal servers:
```bash
dpkg -l | grep -E "xserver-xorg|x11-common"
```

* No screen managements

```bash
dpkg -l | grep -E "gdm3|lightdm|sddm|xdm"
```

* No startx command

```bash
whereis startx
```

---

#### User must not be root

* Show user / root

```bash
whoami
```

* User id

```bash
id
```

* If terminal prompt ends in `$` instead of `#`, it is not root.

---

#### Active services

* UFW service state

```bash
sudo ufw status numbered
```

* SSH service state

```bash
sudo systemctl status ssh
```

* OS is Debian or Rocky

```bash
hostnamectl
```

---

#### User interface

* New user

```bash
sudo adduser <username>
```

* Assign new user to sudo group

```bash
sudo adduser <new_user> sudo
```

* New group

```bash
sudo groupadd <groupname>
```

* Check if group exists

```bash
getent group <groupname>
```

* Add user to group

```bash
sudo usermod -aG <grupo> <username>
```

* Remove user from group

```bash
sudo deluser <username> <groupname>
```

* User groups

```bash
groups <username>
```

* List all users

```bash
cut -d: -f1 /etc/passwd
```

* Delete user

```bash
sudo deluser <username>
sudo deluser --remove-home <username>
sudo deluser --remove-all-files <username>
```

* List all groups

```bash
cut -d: -f1 /etc/group
```

* List users in group sudo

```bash
getent group sudo
```

* Delete group

```bash
sudo groupdel <groupname>
sudo deluser <username> <groupname>
```

* Change user password

```bash
sudo passwd <username>
```

* Lock / unlock a user

```bash
sudo passwd -l <username>
sudo passwd -u <username>
```

* Login under user

```bash
sudo login <username>
```

---

#### Password

* Password complexity rules

```bash
nano /etc/security/pwquality.conf
```

* Password expiration rules

```bash
nano /etc/login.defs
```

* Apply to existing users (including root):

```bash
sudo chage -M 30 <username>
sudo chage -m 2 <username>
sudo chage -W 7 <username>
```

---

#### Machine hostname

* Check hostname

```bash
hostname
```
* Change hostname

```bash
sudo hostnamectl set-hostname <new_hostname>
```

---

#### Partitioning

* MV's partitioning, LVM (Logical Volume Manager) and cryptography

```bash
lsblk
```

* Verify free / used space

```bash
df -h
```

* More details of physical, group, and virtual volumes

```bash
sudo pvdisplay
sudo vgdisplay
sudo lvdisplay
```

---

#### AppArmor

* Check status

```bash
sudo aa-status
```

* Verify if starts on boot

```bash
sudo systemctl status apparmor
```

---

#### SUDO

* Sudo status

```bash
sudo -V
sudo --version
sudo visudo -c
which sudo
```

* Sudo rules

```bash
sudo visudo -f /etc/sudoers.d/42-born2beroot
```

```text
passwd_tries=3: allow at most three password attempts.
badpass_message: display your custom message when authentication fails.
logfile: record sudo activity in the specified log file.
log_input,log_output: enable logging of command input and output.
requiretty: TTY, require sudo to run from a terminal.
secure_path: folders excluded form sudo
```

* Sudo log

```bash
sudo ls -la /var/log/sudo/
```

* Sudo log history (last 5 entrances)

```bash
sudo tail -n 5 /var/log/sudo/sudo.log
```

---

### UFW

* UFW status

```bash
sudo service ufw status
sudo ufw status verbose
dpkg -s ufw
```

* UFW rules

```bash
sudo ufw status numbered
```

* Add new UFW port

```bash
sudo ufw allow <port>
```

* Remove UFW port

```bash
sudo ufw delete <port_number>
```

* Block incoming connections

```bash
ufw default deny incoming
```

* Allow outgoing connections

```bash
ufw default allow outgoing
```

---

#### SSH

* SSH status

```bash
sudo systemctl status ssh
sudo service ssh status
```

* SSH rules

```bash
sudo nano /etc/ssh/sshd_config.d/42-born2beroot.conf
sudo nano /etc/ssh/sshd_config
sudo nano /etc/ssh/ssh_config
```

* On terminal

localhost can be the machine IP 127.0.0.1, 0.0.0.0, or another. Find out:

```bash
ip a
ip route show default
```

* Configure VirtualBox port redirection

```text
Setting		Value
Protocol	TCP
Host IP		127.0.0.1
Host Port	424X
Guest IP	Leave blank
Guest Port	4242
```

* On a machine terminal

```bash
ssh -p 424X <user_login>@<localhost>
```

* No SSH login as root

```bash
login root
ssh -p 424X root@<localhost>
```

---

#### monitoring.sh

* Show / edit

```bash
nano /usr/local/bin/monitoring.sh
```

#### cron

* Cron status

```bash
systemctl status cron
```

#### crontab

* Show / edit

```bash
sudo crontab -e
```

* Verify

```bash
sudo crontab -l
```

```text
*/10 * * * * /usr/local/bin/monitoring.sh
@reboot /usr/local/bin/monitoring.sh
```

---

### Endings

* End session

```bash
exit
```

* Reboot / restart

```bash
sudo reboot
```

* Turn off

```bash
sudo poweroff
```

---

### Small glossary

| Term | Name | Explanation |
|---|---|---|
| apt | advanced package tool | basic and simple Linux package manager |
| aptitude |  | more complex package manager |
| sudo | superuser do | program that allows a regular user to execute commands with root privileges in an auditable manner |
| LVM | logical volume manager | layer between the physical disk and the file system that manages disk storage |
| UFW | uncomplicated firewall | firewall manager that controls network traffic and access authorization |
| SSH | secure shell | encrypted network protocol for managing servers remotely |
| APPArmor | application armor | Linux security module that controls access to resources using users and groups |
| cron | cronos | task-scheduling daemon |
| crontab | cron table | schedule of tasks to be executed by cron |
| wall | write to all | utility used to send a text message to the terminals of all users currently connected to the system. |
| TCP | transmission control protocol | protocol to providin delivery of a stream of octets (bytes) between applications running on hosts communicating via an IP network |


### Checking the disk signature

The project requires a SHA-1 (Secure Hash Algorithm version 1) signature of the virtual disk. On a Windows computer, the hash can be calculated on the .vdi file with:

```powershell
certutil -hashfile "C:\path\to\disk.vdi" SHA1
```

On a Linux computer:

```bash
sha1sum path/to/disk.vdi
```
