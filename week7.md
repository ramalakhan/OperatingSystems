# ASSESSMENT WEEK 7 

# Security Audit and System Evaluation. 


## Network Security Assessment (Nmap)

We need to install nmap on our system . 

``` bash 
# we can use this command to install nmap

sudo apt install nmap -y


```

Verification: 

![](/images/nmap(1).png)


After installing nmap we can use this command to scan for open ports.

``` bash 

sudo nmap -sV -p- localhost

```
![](/images/nmap(scan).png)


## Baseline security Scan 


Installation Documentation 

``` bash 
sudo apt update
sudo apt install lynis -y
sudo lynis audit system

```

### Hardening Score Before


![](/images/hardening(score).png)


### System Hardening

``` bash 

sudo apt apt-listchanges

```
SSH hardening

![](/images/harden(1).png)





## Problem faced during this week 

I faced a major problem when installing nmap, there was no space available in var/cache/apt/archives/

![](/images/problem(1).png)

I tried to install nmap using snap which does not rely on /var but it still did not work as there was no space available in the system. 

![](/images/problem(2).png)



## Solution 

A complex solution to this problem was proposed. 

MAIN PROBLEM : disk size 25 GB and 24 GB aleady in use due to which installation to new tools were rejected. 

SOLUTION 1 : Increase the disk size in virtual machine

The original vdi size was 25Gb which was later increased to 30 GB. 

![](/images/soultion(1).png)

Despite the increase of the virtual disk the problem still showed reason : Disk size was reallocated but disk partion was not set up . 

``` bash 
#to do this we confirmed the disk size by 

lsblk
sudo growpart /dev/sda 2

#this will resize the partition

sudo resize2fs /dev/sda2 

#this will resize the file system 

lsblk

```

![](/images/solution(2).png)


After making these changes we were able to install nmap : 


![](/images/nmap(1).png)

### Hardening after 

![](/images/harden(2).png)



## SSH security Veification 

![](/images/ssh-verification.png)




## CONCLUSION 


This week successfully transitioned the system from performance testing to security hardening. By conducting a mandatory audit using **Lynis** and **Nmap**, I identified and remediated critical vulnerabilities, specifically securing the `/etc/sudoers.d` directory to prevent unauthorized privilege escalation. The final network assessment confirmed a strict firewall policy with only essential ports (22 and 80) open, verifying that the server is now both performance-optimized and security-hardened for production use.
