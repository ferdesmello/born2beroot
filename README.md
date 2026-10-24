_This project has been created as part of the 42 curriculum by ferde-so._

# Born2Beroot

## Description

Born2Beroot is a system administration project from the 42 curriculum. Its goal is to create and configure a secure virtual machine according to a strict set of requirements.

The project introduces virtualization, Linux system administration, disk encryption, user and group management, password policies, SSH, firewall configuration, sudo (super user do) privileges, and system monitoring.

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

- Born2beroot subject
- [Debian](https://www.debian.org/)
- [Wikipedia](https://wikipedia.org/)
- [reddit](https://www.reddit.com/)
- [VirtualBox](https://www.virtualbox.org/)

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

The following commands were useful for checking the system configuration. They can also be used during evaluation to demonstrate that specific requirements are satisfied.

| Requirement | Command |
|---|---|
| Hostname | `hostnamectl` |
| Current user and groups | `id <user_name>` |
| Operating system and kernel | `uname -a` and `cat /etc/os-release` |
| SSH service status | `sudo systemctl status ssh` |
| SSH port and root-login policy | `sudo sshd -T \| grep -E '^(port\|permitrootlogin) '` |
| Firewall status | `sudo ufw status verbose` |
| Password-aging policy | `sudo chage -l <user_name>` |
| Sudo configuration syntax | `sudo visudo -c` |
| Sudo privileges | `sudo -l` |
| AppArmor status | `sudo systemctl is-active apparmor` |
| Encrypted container status | `sudo cryptsetup status sda5_crypt` |
| Logical volumes and mounts | `lsblk -o NAME,TYPE,FSTYPE,MOUNTPOINTS` |
| Cron schedule | `sudo crontab -l` |
| Monitoring script syntax | `bash -n /usr/local/bin/monitoring.sh` |

### Checking the disk signature

The project requires a SHA-1 (Secure Hash Algorithm version 1) signature of the virtual disk. On a Windows computer, the hash can be calculated with:

```powershell
certutil -hashfile "C:\path\to\disk.vdi" SHA1
```

On a Linux computer:

```bash
sha1sum path/to/disk.vdi
```

Compare the resulting hash with the value stored in `signature.txt`. Make sure to hash the exact virtual disk required by the evaluation procedure, not the `.vbox` configuration file or another copy of the disk.
