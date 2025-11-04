
---

# Linux Server and Client – Full Configuration Guide

## 1. Description

This file demonstrates the **installation and configuration of a secure, multi-user Linux server** using **Ubuntu Server 24.04.3 LTS** in a **virtualized environment (Oracle VirtualBox)**.

The goal is to build a **reliable, secure, and scalable networked environment** from scratch — focusing on:

* Security
* User and group management
* Network and service configuration

### Final Deployment Overview

The server provides:

* **DHCP** and **DNS** services
* **Secure file access** via user and group permissions
* A **client VM** on an **isolated internal network**

---

## 2. Server Libraries & Dependencies Installed

Below are all essential packages installed after the base OS setup.

```bash
# Refresh package list
sudo apt update

# Interactive system monitor
sudo apt install htop -y

# Intrusion prevention (protects SSH)
sudo apt install fail2ban -y

# Automatic security updates
sudo apt install unattended-upgrades -y

# Lightweight DHCP + DNS server
sudo apt install dnsmasq -y
```

---

## 3. Ubuntu Server Configuration (Step-by-Step)

### 3.1. Initial System Setup

```bash
# Upgrade all existing packages
sudo apt upgrade -y
```

---

### 3.2. User & Group Management

```bash
# Create a group
sudo groupadd developers

# Create a user and add to 'developers'
sudo useradd -m -G developers bwayne

# Set password
sudo passwd bwayne

# Grant sudo privileges
sudo usermod -aG sudo bwayne
```

---

### 3.3. Basic Firewall (UFW)

```bash
# Allow SSH
sudo ufw allow OpenSSH

# Enable firewall
sudo ufw enable

# Check status
sudo ufw status
```

---

### 3.4. Secure SSH Configuration

Edit SSH configuration:

```bash
sudo nano /etc/ssh/sshd_config
```

Find and modify:

```bash
PermitRootLogin no
```

Then restart the service:

```bash
sudo systemctl restart ssh
```

---

### 3.5. Directory & Permission Management

```bash
# Create directories
sudo mkdir -p /srv/projects/shared_project
sudo mkdir /srv/private

# Set group ownership
sudo chgrp developers /srv/projects/shared_project

# Permissions
sudo chmod 770 /srv/projects/shared_project
sudo chmod 700 /srv/private

# Inherit group ownership
sudo chmod g+s /srv/projects/shared_project

# Verify
ls -ld /srv/projects/shared_project
ls -ld /srv/private
```

---

### 3.6. Server Network Configuration (Static IP)

The server uses **two network adapters**:

* **Adapter 1 (enp0s3):** NAT (Internet access)
* **Adapter 2 (enp0s8):** Internal Network (project LAN)

Edit the Netplan config:

```bash
sudo nano /etc/netplan/01-netcfg.yaml
```

Replace contents with:

```yaml
# /etc/netplan/01-netcfg.yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: no
      addresses:
        - 10.10.10.1/24
```

Apply configuration:

```bash
sudo netplan apply
```

---

### 3.7. DHCP & DNS Server (dnsmasq)

Open config:

```bash
sudo nano /etc/dnsmasq.conf
```

Append:

```conf
# /etc/dnsmasq.conf
interface=enp0s8
domain=project.lan
dhcp-range=10.10.10.100,10.10.10.200,12h
dhcp-option=option:router,10.10.10.1
dhcp-option=option:dns-server,10.10.10.1
```

Edit `/etc/hosts`:

```bash
sudo nano /etc/hosts
```

Add entries:

```conf
127.0.0.1   localhost
127.0.1.1   serverubuntudar1.project.lan serverubuntudar1
10.10.10.1  serverubuntudar1.project.lan serverubuntudar1
```

Open firewall ports:

```bash
sudo ufw allow 67/udp
sudo ufw allow 53/udp
sudo ufw allow 53/tcp
```

---

### 3.8. Troubleshooting & Service Management

#### Port 53 “Address already in use”

```bash
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
sudo rm /etc/resolv.conf
sudo nano /etc/resolv.conf
```

Add:

```conf
nameserver 8.8.8.8
```

Then:

```bash
sudo systemctl start dnsmasq
sudo systemctl enable dnsmasq
```

---

#### ⚠️ “sudo: unable to resolve host” Fix

1. Reboot → select **Recovery Mode**
2. Choose **root shell**
3. Mount filesystem:

   ```bash
   mount -o remount,rw /
   ```
4. Edit hosts file:

   ```bash
   nano /etc/hosts
   ```
5. Reboot again:

   ```bash
   reboot
   ```

---

#### Fix `dnsmasq.leases` Error

```bash
sudo touch /var/lib/dnsmasq/dnsmasq.leases
sudo chown dnsmasq:dnsmasq /var/lib/dnsmasq/dnsmasq.leases
sudo systemctl restart dnsmasq
```

---

## 4. Ubuntu Client Configuration & Demo

**VirtualBox Network Setup:**

* **Adapter 1:** Internal Network (`project-lan`)
* **IP Mode:** Automatic (DHCP)

---

### 4.1. Verification Commands (Run on Client)

```bash
# Check assigned IP (should be in 10.10.10.x range)
ip a

# Check DNS settings
cat /etc/resolv.conf

# Ping server by name
ping serverubuntudar1

# SSH into server
ssh bwayne@10.10.10.1
```

**Inside SSH session:**

```bash
# Should succeed
cd /srv/projects/shared_project

# Should fail (permission denied)
cd /srv/private
```

**Force DHCP renewal (for testing):**

```bash
sudo dhclient -r
sudo dhclient
```

---

## 🧾 Summary

| Component          | Configuration                      |
| ------------------ | ---------------------------------- |
| **OS**             | Ubuntu Server 24.04.3 LTS          |
| **Services**       | SSH, UFW, DHCP, DNS                |
| **Security**       | Fail2Ban, UFW, root login disabled |
| **Network**        | 10.10.10.0/24 internal LAN         |
| **Users**          | `bwayne` (developer, sudo)         |
| **Shared Folder**  | `/srv/projects/shared_project`     |
| **Private Folder** | `/srv/private`                     |

---

## 🏁 Final Result

* ✅ Server provides DHCP & DNS to clients
* ✅ Clients can connect via SSH
* ✅ User/group-based directory permissions enforced
* ✅ Network isolated and secure

---

**By:** *Darl Ellison Floresca*
**Used:** Ubuntu Server 24.04.3 LTS (VirtualBox)

---
