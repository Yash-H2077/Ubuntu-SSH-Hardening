# Ubuntu-SSH-Hardening
A reproducible lab environment for hardening SSH on an Ubuntu server running on docker 

## Tools:
 - Kali LInux
 - Docker
 - Fail2ban
 - tcpdump
 - UFW
## Docker setup
### 1. Create a dockerfile and paste the following code inside:
```
FROM ubuntu:22.04

RUN apt update && apt install -y \
    openssh-server \
    fail2ban \
    iproute2 \
    iputils-ping \
    net-tools \
    sudo \
    curl \
    nano \
    tcpdump

# Create SSH directory and user
RUN useradd -m yash && echo 'yash:yashpass' | chpasswd && \
    mkdir /var/run/sshd && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config && \
    sed -i 's/PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
```
##### this will create a docker file and preset rules and values
### 2. Build and Run the Container
```bash
docker build -t ubuntu-ssh-hardened .
docker run -d --name sshlab -p 2222:22 ubuntu-ssh-hardened
```
## Methodology:
### Step 1:Update /etc/ssh_config and paste the following  
 - PermitRootLogin no
 - PasswordAuthentication no
 - PubkeyAuthentication yes

### Step 2: Restart ssh
```bash
service ssh restart
```
### Step 3: Configure Fail2ban
Edit /etc/fail2ban/jail.local
```bash
[sshd]
enabled = true
port = ssh
logpath = /var/log/auth.log
maxretry = 3
```
  #### Start Fail2ban:
   ```bash
   service fail2ban start
   ```
  #### Check status:
   ```bash
   fail2ban-client status sshd
   ```
### Step 4: Capture Network Traffic
#### Perform before hardening on your host device    
```bash
tcpdump -i any port 22 -w before.pcap
```
#### Perform after hardening on your host device 
```bash
tcpdump -i any port 22 -w after.pcap
```
#### To catch traffic do multiple login attempts and compare

### Step 5: UFW Config(on Kali Machine)
 #### Install and enable UFW  
 ```bashh
 sudo apt install ufw
 sudo ufw enable
 ```
 #### Deny Incoming and Allow Outgoing
 ```bash
 sudo ufw default deny incoming
 sudo ufw default allow outgoing
 ``` 
 #### Verfiy
 ```bash
 sudo ufw status verbose
 ```

## Notes
 - Be careful while editing the config files and edit with precision
 - Make sure to restart the services after editing them
 - Name the .pcap files and screenshots taken to avoid 

## Screenshots 
Refer to the above screenshots for better explanation and understanding 
