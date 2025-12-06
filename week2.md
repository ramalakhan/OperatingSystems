# Assessment week 2

## Introduction 

In this assessment week, we will use various test to evalute the system performance and use ssh for remote connections, all remote testing will be done via ssh. 


## 1 Performance Testing Plan with Remote Monitoring Methodology and Testing Approach.

### 1.1 Performance Testing plan 

This plan describes the methodology for performance testing our chosen debain-based linux OS and the details for remote monitoring approach.

### 1.1 Tools for testing the system performance

1. htop
2. iotop
3. nmon

### 1.2 Installing tools in both systems.

Since our package manager in both systems are the same which is apt, we can install the tools using the command below.

sudo apt install htop -y 
sudo apt install iotop -y
sudo apt install nmon -y 

after installing these tools we can observe the system in different states.

### 1.3 Idle state ( server )

### Image server idle state using htop

![](/server_idle_state.png)

NOTE : we can use the btop tool which give us more interactive version of the htop even though htop itself is a interactive version of top we can use btop for even more better visual representation of the system. 


### Image Server idle state using btop 

![](/server_idle_state_btop.png)



### Observation

we can observe the machine state in idle state , the CPU usages is 0% and memory usage is also very low at only 6% whereas the Disk usage is at 16%


### 1.4 Observation during updates(server)

![](/update(server).png)

During updates we were able to see a spike in the CPU usages and network usages. 

### 1.3 Idle state (Workstation)

### Image Workstation in idle state usign htop. 

![](/workstation_idle_state_using_htop.png)


### Image Workstation in idle state using btop.

![](/workstation_idle_state(2).png)

### Observation 

There is a quite interesting information which we can observe in workstation, the CPU usage is not stable some cores are at max 10% usages whereas some are at 1% to 3% . Overall the CPU usages are low but it is still fluctuating compared to our server CPU usage. 

The memory usage is at 34% and disk is about 50%. 

It is quite noticeble that there is more overall usage in GUI system ( Workstation ) than our server which is CLI based.


### 1.4 Observation during updates(Workstation)

![](/workstatioN(update).png)

During updates we were able to see a 100% spike in one of the core in  CPU usages and network usages. 



## 2 . Remote Monitoring

For remote monitoring we will utilize ssh which we can use to connect to server from the workstatiom . We need to make some initial configuration in the machine first which includes

1. Intalling openssh-server in the server

sudo apt install openssh-server -y

2. Check the status of the sshd service

sudo systemctl status ssh

Note : if is does not shows active and running we can use the command : sudo systemctl start ssh 

3. Enable ufw firewall 

sudo ufw enable

4. Allow ssh in firewall

sudo ufw allow ssh 


After using the steps mentioned above we can securely ssh in to our server from the workstation using the command : sudo ssh ram@10.0.0.2


### Successful Remote SSH connection 

![](/ssh(login).png)



## 3 . Security Configuration

In this assessment week, we will do a basic security configuration however in assessment week 4,5 we will make complex configuration on  MAC ( mandatory access control), SSH using key-based authentication and advanced firewall rules with other IDS and IPS implementation. 


## 3.1 Network Security 

we will configure ufw (uncomplicated firewall ) to deny all incoming traffic and allow outgoing traffic which can be done using the command. 

sudo ufw default deny incoming
sudo ufw default allow outgoing

we can check the firewall status using sudo ufw status verbose

### Image : firewall configuration 

![](/firewall(1).png)


## 3.2 SSH hardening

We can harden our ssh using key based authentication and we can also configure our firewall to allow ssh from one particular ip only (Workstation), by doing these we are following the defence in depth strategy where we are putting extra layer of security on top to secure our system.

We can make some changes in the sshd_config file which is by default located at etc/ssh/sshd_config.

**Changes**
1. PasswordAuthentication no

This settings will disable password based authentication. 

![](/ssh_harden(1).png)

2. Port (custom port)

This settings will change the default port for ssh.

![](/ssh_harden(2).png)


## 3.2 Automatic Updates

We can set up automatic updates in our system using the package unattended-upgrades

We can install this package using the command : sudo apt install unattended-upgrades -y

We can enable the service using : 

sudo dpkg-reconfigure --priority=low unattended-upgrades

(enter yes when prompted in the terminal)

We can validate the service using :

sudo systemctl list-timers apt-daily*

![](/automatic_updates.png)


## 3.3 Security (IPS)

We can use install a host-based IPS called fail2ban which will monitor for authentication logs for unauthorized login attempts.

**Setup**

sudo apt install fail2ban -y

**configuration**

The default configuration is sufficient to start protecting ssh, all configuration is done in the /etc/fail2ban/jail.local


