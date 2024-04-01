# Secure server

2023-01-18

Basics
---
```bash
# Update packages
sudo apt-get update
sudo apt-get upgrade
sudo apt-get autoremove
sudo apt-get autoclean
# Auto update packages
sudo apt-get install unattended-upgrades
sudo dpkg-reconfigure -plow unattended-upgrades
# Create user, add it to sudoers
useradd -G sudo -m <USER> -s /bin/bash
# Change user password
passwd <USER>
# Copy .ssh from root to user
cp -R /root/.ssh /home/<USER>/
# Setting permissions
chown <USER>:<USER> -R /home/<USER>/.ssh
chmod 600 /home/<USER>/.ssh
```

Secure ssh configuration
---
```bash
# Change port to prevent auto scanning from bots
Port 88 
# Time in which user must authenticate after starting connection
LoginGraceTime 20
# Disable root login
PermitRootLogin no
# Self-explainatory
MaxAuthTries 3
# Self-explainatory
PasswordAuthentication no
# Self-explainatory
PermitEmptyPasswords no
# Disable not needed auth methods
ChallengeResponseAuthentication no
KerberosAuthentication no
GSSAPIAuthentication no
# Disable X11 tunelling. Hardly ever used, better to disable it
X11Forwarding no
# Disable other unnecessary options
AllowAgentForwarding no
AllowTcpForwarding no
PermitTunnel no]
# Disable banner on start
Banner none
# Timeout on session 5 minutes, 2 retries
ClientAliveInterval 300
ClientAliveCountMax 2
```

Fail2Ban
---
```bash
# Install
apt-get install fail2ban
# Copy config
cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

Configuring `/etc/fail2ban/jail.local`

```bash
. . .
[sshd]

1enabled  = true
port    = ssh
logpath = %(sshd_log)s
. . .
```

Restart:
```bash
service fail2ban restart
```

2FA
---
```bash
sudo apt-get install libpam-google-authenticator
```
Add at end of file `/etc/pam.d/sshd`
```bash
. . .
auth required pam_google_authenticator.so
. . .
```
```
sudo systemctl restart sshd.service
```

Edit ssh config sshd
```bash
UsePAM yes
ChallengeResponseAuthentication yes
AuthenticationMethods publickey,password publickey,keyboard-interactive
```
Comment in file `/etc/pam.d/sshd`
```bash
. . .
#@include common-auth
. . .
```
```
sudo systemctl restart sshd.service
```

References
---

1. [How To Harden OpenSSH on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-harden-openssh-on-ubuntu-18-04)
2. [Docker container management with Traefik v2 and Portainer](https://rafrasenberg.com/posts/docker-container-management-with-traefik-v2-and-portainer/)
3. [Hardening SSH](https://medium.com/@jasonrigden/hardening-ssh-1bcb99cd4cef)
4. [How To Set Up Multi-Factor Authentication for SSH on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-multi-factor-authentication-for-ssh-on-ubuntu-16-04)

