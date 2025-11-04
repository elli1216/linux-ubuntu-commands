<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif; line-height: 1.6; color: #24292e; max-width: 900px; margin: 0 auto; padding: 20px;">

<div style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); padding: 30px; border-radius: 10px; margin-bottom: 30px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">

<h1 style="color: #ffffff; margin: 0; font-size: 2.5em; text-shadow: 2px 2px 4px rgba(0,0,0,0.2);">Linux Server and Client – Full Configuration Guide</h1>

</div>

<div style="background-color: #f8f9fa; padding: 25px; border-radius: 8px; border-left: 5px solid #667eea; margin-bottom: 30px; box-shadow: 0 2px 4px rgba(0,0,0,0.05);">

<h2 style="color: #667eea; margin-top: 0; font-size: 1.8em; border-bottom: 2px solid #667eea; padding-bottom: 10px;">1. Description</h2>

<p style="font-size: 1.1em; color: #2c3e50; margin-bottom: 15px;">This file demonstrates the <strong style="color: #667eea;">installation and configuration of a secure, multi-user Linux server</strong> using <strong style="color: #667eea;">Ubuntu Server 24.04.3 LTS</strong> in a <strong style="color: #667eea;">virtualized environment (Oracle VirtualBox)</strong>.</p>

<p style="font-size: 1.1em; color: #2c3e50; margin-bottom: 15px;">The goal is to build a <strong style="color: #667eea;">reliable, secure, and scalable networked environment</strong> from scratch — focusing on:</p>

<ul style="font-size: 1.05em; color: #2c3e50; padding-left: 25px;">
<li style="margin-bottom: 8px;"><strong>Security</strong></li>
<li style="margin-bottom: 8px;"><strong>User and group management</strong></li>
<li style="margin-bottom: 8px;"><strong>Network and service configuration</strong></li>
</ul>

<h3 style="color: #764ba2; margin-top: 25px; font-size: 1.4em;">Final Deployment Overview</h3>

<p style="font-size: 1.05em; color: #2c3e50;">The server provides:</p>

<ul style="font-size: 1.05em; color: #2c3e50; padding-left: 25px;">
<li style="margin-bottom: 8px;"><strong style="color: #667eea;">DHCP</strong> and <strong style="color: #667eea;">DNS</strong> services</li>
<li style="margin-bottom: 8px;"><strong style="color: #667eea;">Secure file access</strong> via user and group permissions</li>
<li style="margin-bottom: 8px;">A <strong style="color: #667eea;">client VM</strong> on an <strong style="color: #667eea;">isolated internal network</strong></li>
</ul>

</div>

<div style="background-color: #f8f9fa; padding: 25px; border-radius: 8px; border-left: 5px solid #28a745; margin-bottom: 30px; box-shadow: 0 2px 4px rgba(0,0,0,0.05);">

<h2 style="color: #28a745; margin-top: 0; font-size: 1.8em; border-bottom: 2px solid #28a745; padding-bottom: 10px;">2. Server Libraries & Dependencies Installed</h2>

<p style="font-size: 1.05em; color: #2c3e50; margin-bottom: 15px;">Below are all essential packages installed after the base OS setup.</p>

<div style="background-color: #1e1e1e; padding: 20px; border-radius: 8px; overflow-x: auto; margin: 20px 0; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">

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

</div>

</div>

<div style="background-color: #f8f9fa; padding: 25px; border-radius: 8px; border-left: 5px solid #ff6b6b; margin-bottom: 30px; box-shadow: 0 2px 4px rgba(0,0,0,0.05);">

<h2 style="color: #ff6b6b; margin-top: 0; font-size: 1.8em; border-bottom: 2px solid #ff6b6b; padding-bottom: 10px;">3. Ubuntu Server Configuration (Step-by-Step)</h2>

<div style="background-color: #ffffff; padding: 20px; border-radius: 6px; margin: 20px 0; border: 1px solid #e1e4e8;">

<h3 style="color: #ff6b6b; margin-top: 0; font-size: 1.3em;">3.1. Initial System Setup</h3>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
# Upgrade all existing packages
sudo apt upgrade -y
```

</div>

</div>

<div style="background-color: #ffffff; padding: 20px; border-radius: 6px; margin: 20px 0; border: 1px solid #e1e4e8;">

<h3 style="color: #ff6b6b; margin-top: 0; font-size: 1.3em;">3.2. User & Group Management</h3>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

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

</div>

</div>

<div style="background-color: #ffffff; padding: 20px; border-radius: 6px; margin: 20px 0; border: 1px solid #e1e4e8;">

<h3 style="color: #ff6b6b; margin-top: 0; font-size: 1.3em;">3.3. Basic Firewall (UFW)</h3>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
# Allow SSH
sudo ufw allow OpenSSH

# Enable firewall
sudo ufw enable

# Check status
sudo ufw status
```

</div>

</div>

<div style="background-color: #ffffff; padding: 20px; border-radius: 6px; margin: 20px 0; border: 1px solid #e1e4e8;">

<h3 style="color: #ff6b6b; margin-top: 0; font-size: 1.3em;">3.4. Secure SSH Configuration</h3>

<p style="color: #2c3e50; margin-bottom: 10px;">Edit SSH configuration:</p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
sudo nano /etc/ssh/sshd_config
```

</div>

<p style="color: #2c3e50; margin: 15px 0;">Find and modify:</p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
PermitRootLogin no
```

</div>

<p style="color: #2c3e50; margin: 15px 0;">Then restart the service:</p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
sudo systemctl restart ssh
```

</div>

</div>

<div style="background-color: #ffffff; padding: 20px; border-radius: 6px; margin: 20px 0; border: 1px solid #e1e4e8;">

<h3 style="color: #ff6b6b; margin-top: 0; font-size: 1.3em;">3.5. Directory & Permission Management</h3>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

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

</div>

</div>

<div style="background-color: #ffffff; padding: 20px; border-radius: 6px; margin: 20px 0; border: 1px solid #e1e4e8;">

<h3 style="color: #ff6b6b; margin-top: 0; font-size: 1.3em;">3.6. Server Network Configuration (Static IP)</h3>

<p style="color: #2c3e50; margin-bottom: 15px;">The server uses <strong style="color: #667eea;">two network adapters</strong>:</p>

<ul style="color: #2c3e50; padding-left: 25px; margin-bottom: 15px;">
<li style="margin-bottom: 8px;"><strong style="color: #667eea;">Adapter 1 (enp0s3):</strong> NAT (Internet access)</li>
<li style="margin-bottom: 8px;"><strong style="color: #667eea;">Adapter 2 (enp0s8):</strong> Internal Network (project LAN)</li>
</ul>

<p style="color: #2c3e50; margin-bottom: 10px;">Edit the Netplan config:</p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
sudo nano /etc/netplan/01-netcfg.yaml
```

</div>

<p style="color: #2c3e50; margin: 15px 0;">Replace contents with:</p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

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

</div>

<p style="color: #2c3e50; margin: 15px 0;">Apply configuration:</p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
sudo netplan apply
```

</div>

</div>

<div style="background-color: #ffffff; padding: 20px; border-radius: 6px; margin: 20px 0; border: 1px solid #e1e4e8;">

<h3 style="color: #ff6b6b; margin-top: 0; font-size: 1.3em;">3.7. DHCP & DNS Server (dnsmasq)</h3>

<p style="color: #2c3e50; margin-bottom: 10px;">Open config:</p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
sudo nano /etc/dnsmasq.conf
```

</div>

<p style="color: #2c3e50; margin: 15px 0;">Append:</p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```conf
# /etc/dnsmasq.conf
interface=enp0s8
domain=project.lan
dhcp-range=10.10.10.100,10.10.10.200,12h
dhcp-option=option:router,10.10.10.1
dhcp-option=option:dns-server,10.10.10.1
```

</div>

<p style="color: #2c3e50; margin: 15px 0;">Edit <code style="background-color: #f1f3f5; padding: 2px 6px; border-radius: 3px; color: #d73a49; font-family: 'Courier New', monospace;">/etc/hosts</code>:</p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
sudo nano /etc/hosts
```

</div>

<p style="color: #2c3e50; margin: 15px 0;">Add entries:</p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```conf
127.0.0.1   localhost
127.0.1.1   serverubuntudar1.project.lan serverubuntudar1
10.10.10.1  serverubuntudar1.project.lan serverubuntudar1
```

</div>

<p style="color: #2c3e50; margin: 15px 0;">Open firewall ports:</p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
sudo ufw allow 67/udp
sudo ufw allow 53/udp
sudo ufw allow 53/tcp
```

</div>

</div>

<div style="background-color: #ffffff; padding: 20px; border-radius: 6px; margin: 20px 0; border: 1px solid #e1e4e8;">

<h3 style="color: #ff6b6b; margin-top: 0; font-size: 1.3em;">3.8. Troubleshooting & Service Management</h3>

<div style="background-color: #fff3cd; padding: 15px; border-radius: 6px; border-left: 4px solid #ffc107; margin: 15px 0;">

<h4 style="color: #856404; margin-top: 0; font-size: 1.1em;">Port 53 "Address already in use"</h4>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
sudo rm /etc/resolv.conf
sudo nano /etc/resolv.conf
```

</div>

<p style="color: #856404; margin: 15px 0;">Add:</p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```conf
nameserver 8.8.8.8
```

</div>

<p style="color: #856404; margin: 15px 0;">Then:</p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
sudo systemctl start dnsmasq
sudo systemctl enable dnsmasq
```

</div>

</div>

<div style="background-color: #f8d7da; padding: 15px; border-radius: 6px; border-left: 4px solid #dc3545; margin: 15px 0;">

<h4 style="color: #721c24; margin-top: 0; font-size: 1.1em;">"sudo: unable to resolve host" Fix</h4>

<ol style="color: #721c24; padding-left: 25px;">
<li style="margin-bottom: 10px;">Reboot → select <strong>Recovery Mode</strong></li>
<li style="margin-bottom: 10px;">Choose <strong>root shell</strong></li>
<li style="margin-bottom: 10px;">Mount filesystem:
   <div style="background-color: #1e1e1e; padding: 10px; border-radius: 4px; overflow-x: auto; margin: 10px 0;">
   ```bash
   mount -o remount,rw /
   ```
   </div>
</li>
<li style="margin-bottom: 10px;">Edit hosts file:
   <div style="background-color: #1e1e1e; padding: 10px; border-radius: 4px; overflow-x: auto; margin: 10px 0;">
   ```bash
   nano /etc/hosts
   ```
   </div>
</li>
<li style="margin-bottom: 10px;">Reboot again:
   <div style="background-color: #1e1e1e; padding: 10px; border-radius: 4px; overflow-x: auto; margin: 10px 0;">
   ```bash
   reboot
   ```
   </div>
</li>
</ol>

</div>

<div style="background-color: #d1ecf1; padding: 15px; border-radius: 6px; border-left: 4px solid #17a2b8; margin: 15px 0;">

<h4 style="color: #0c5460; margin-top: 0; font-size: 1.1em;">Fix <code style="background-color: #f1f3f5; padding: 2px 6px; border-radius: 3px; color: #d73a49; font-family: 'Courier New', monospace;">dnsmasq.leases</code> Error</h4>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
sudo touch /var/lib/dnsmasq/dnsmasq.leases
sudo chown dnsmasq:dnsmasq /var/lib/dnsmasq/dnsmasq.leases
sudo systemctl restart dnsmasq
```

</div>

</div>

</div>

</div>

<div style="background-color: #f8f9fa; padding: 25px; border-radius: 8px; border-left: 5px solid #17a2b8; margin-bottom: 30px; box-shadow: 0 2px 4px rgba(0,0,0,0.05);">

<h2 style="color: #17a2b8; margin-top: 0; font-size: 1.8em; border-bottom: 2px solid #17a2b8; padding-bottom: 10px;">4. Ubuntu Client Configuration & Demo</h2>

<p style="font-size: 1.05em; color: #2c3e50; margin-bottom: 15px;"><strong style="color: #17a2b8;">VirtualBox Network Setup:</strong></p>

<ul style="font-size: 1.05em; color: #2c3e50; padding-left: 25px; margin-bottom: 20px;">
<li style="margin-bottom: 8px;"><strong style="color: #667eea;">Adapter 1:</strong> Internal Network (<code style="background-color: #f1f3f5; padding: 2px 6px; border-radius: 3px; color: #d73a49; font-family: 'Courier New', monospace;">project-lan</code>)</li>
<li style="margin-bottom: 8px;"><strong style="color: #667eea;">IP Mode:</strong> Automatic (DHCP)</li>
</ul>

<div style="background-color: #ffffff; padding: 20px; border-radius: 6px; margin: 20px 0; border: 1px solid #e1e4e8;">

<h3 style="color: #17a2b8; margin-top: 0; font-size: 1.3em;">4.1. Verification Commands (Run on Client)</h3>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

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

</div>

<p style="color: #2c3e50; margin: 15px 0;"><strong style="color: #667eea;">Inside SSH session:</strong></p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
# Should succeed
cd /srv/projects/shared_project

# Should fail (permission denied)
cd /srv/private
```

</div>

<p style="color: #2c3e50; margin: 15px 0;"><strong style="color: #667eea;">Force DHCP renewal (for testing):</strong></p>

<div style="background-color: #1e1e1e; padding: 15px; border-radius: 6px; overflow-x: auto; margin: 15px 0;">

```bash
sudo dhclient -r
sudo dhclient
```

</div>

</div>

</div>

<div style="background-color: #e8f5e9; padding: 25px; border-radius: 8px; border-left: 5px solid #4caf50; margin-bottom: 30px; box-shadow: 0 2px 4px rgba(0,0,0,0.05);">

<h2 style="color: #2e7d32; margin-top: 0; font-size: 1.8em; border-bottom: 2px solid #4caf50; padding-bottom: 10px;">Summary</h2>

<table style="width: 100%; border-collapse: collapse; margin: 20px 0; background-color: #ffffff; border-radius: 6px; overflow: hidden; box-shadow: 0 2px 4px rgba(0,0,0,0.1);">
<thead>
<tr style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);">
<th style="padding: 15px; text-align: left; color: #ffffff; font-weight: 600; border-bottom: 2px solid #764ba2;">Component</th>
<th style="padding: 15px; text-align: left; color: #ffffff; font-weight: 600; border-bottom: 2px solid #764ba2;">Configuration</th>
</tr>
</thead>
<tbody>
<tr style="border-bottom: 1px solid #e1e4e8;">
<td style="padding: 12px 15px; color: #2c3e50; font-weight: 600;"><strong>OS</strong></td>
<td style="padding: 12px 15px; color: #2c3e50;">Ubuntu Server 24.04.3 LTS</td>
</tr>
<tr style="background-color: #f8f9fa; border-bottom: 1px solid #e1e4e8;">
<td style="padding: 12px 15px; color: #2c3e50; font-weight: 600;"><strong>Services</strong></td>
<td style="padding: 12px 15px; color: #2c3e50;">SSH, UFW, DHCP, DNS</td>
</tr>
<tr style="border-bottom: 1px solid #e1e4e8;">
<td style="padding: 12px 15px; color: #2c3e50; font-weight: 600;"><strong>Security</strong></td>
<td style="padding: 12px 15px; color: #2c3e50;">Fail2Ban, UFW, root login disabled</td>
</tr>
<tr style="background-color: #f8f9fa; border-bottom: 1px solid #e1e4e8;">
<td style="padding: 12px 15px; color: #2c3e50; font-weight: 600;"><strong>Network</strong></td>
<td style="padding: 12px 15px; color: #2c3e50;">10.10.10.0/24 internal LAN</td>
</tr>
<tr style="border-bottom: 1px solid #e1e4e8;">
<td style="padding: 12px 15px; color: #2c3e50; font-weight: 600;"><strong>Users</strong></td>
<td style="padding: 12px 15px; color: #2c3e50;"><code style="background-color: #f1f3f5; padding: 2px 6px; border-radius: 3px; color: #d73a49; font-family: 'Courier New', monospace;">bwayne</code> (developer, sudo)</td>
</tr>
<tr style="background-color: #f8f9fa; border-bottom: 1px solid #e1e4e8;">
<td style="padding: 12px 15px; color: #2c3e50; font-weight: 600;"><strong>Shared Folder</strong></td>
<td style="padding: 12px 15px; color: #2c3e50;"><code style="background-color: #f1f3f5; padding: 2px 6px; border-radius: 3px; color: #d73a49; font-family: 'Courier New', monospace;">/srv/projects/shared_project</code></td>
</tr>
<tr style="border-bottom: 1px solid #e1e4e8;">
<td style="padding: 12px 15px; color: #2c3e50; font-weight: 600;"><strong>Private Folder</strong></td>
<td style="padding: 12px 15px; color: #2c3e50;"><code style="background-color: #f1f3f5; padding: 2px 6px; border-radius: 3px; color: #d73a49; font-family: 'Courier New', monospace;">/srv/private</code></td>
</tr>
</tbody>
</table>

</div>

<div style="background: linear-gradient(135deg, #4caf50 0%, #45a049 100%); padding: 25px; border-radius: 8px; margin-bottom: 30px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">

<h2 style="color: #ffffff; margin-top: 0; font-size: 1.8em; text-shadow: 1px 1px 2px rgba(0,0,0,0.2);">Final Result</h2>

<ul style="color: #ffffff; font-size: 1.1em; padding-left: 25px; margin: 0;">
<li style="margin-bottom: 10px; text-shadow: 1px 1px 2px rgba(0,0,0,0.1);">Server provides DHCP & DNS to clients</li>
<li style="margin-bottom: 10px; text-shadow: 1px 1px 2px rgba(0,0,0,0.1);">Clients can connect via SSH</li>
<li style="margin-bottom: 10px; text-shadow: 1px 1px 2px rgba(0,0,0,0.1);">User/group-based directory permissions enforced</li>
<li style="margin-bottom: 10px; text-shadow: 1px 1px 2px rgba(0,0,0,0.1);">Network isolated and secure</li>
</ul>

</div>

<div style="background-color: #f8f9fa; padding: 20px; border-radius: 8px; text-align: center; border-top: 3px solid #667eea; margin-top: 30px;">

<p style="color: #6a737d; margin: 5px 0; font-size: 0.95em;"><strong style="color: #667eea;">By:</strong> <em style="color: #2c3e50;">Darl Ellison Floresca</em></p>
<p style="color: #6a737d; margin: 5px 0; font-size: 0.95em;"><strong style="color: #667eea;">Used:</strong> <span style="color: #2c3e50;">Ubuntu Server 24.04.3 LTS (VirtualBox)</span></p>

</div>

</div>
