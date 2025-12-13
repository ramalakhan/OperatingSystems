# ASSESSMENT WEEK 5 
# ADVANCED SECURITY AND MONITORING INFRASTRUCTURE.

## Introduction

In this week we will implement and enforce MAC and configure our system for automatic updates, Intrusion prevention system and we will also write some script with bash.


## Implementing Access Control Using AppArmor

In ubuntu we can use Apparmor to enforce MAC. To verify the status of apparmor

``` bash 

# verify the status of app armor
sudo systemctl status apparmor

```

![](/images/app-armor-status.png)


### Installing Management Utilities 

```
 bash 

sudo apt update
sudo apt install apparmor-utils -y


```

we can also view the current profiles that are logged in using the command : 

``` bash

sudo aa-status
```

![](/images/sudo-aa-status.png)


## Difference Between Complain Mode and Enforce Mode

| Feature | Complain Mode | Enforce Mode |
|-------|---------------|--------------|
| Policy Enforcement | Does NOT block actions | Blocks actions that violate policy |
| Logging | Logs policy violations only | Logs and blocks policy violations |
| Use Case | Used for testing and learning profiles | Used for active system protection |


## Reporting 

Reporting can be done via the command : 

``` bash 

sudo aa-status --verbose

```


## Automatic Updates

We can configure automatic updates using the tool called unattended-upgrades. 


## Installation documentation 

``` bash 

sudo apt update

#installs unattended-upgrades tool. 
sudo apt install unattended-upgrades -y 

# Enabling the service

sudo dpkg-reconfigure -plow unattended-upgrades

#verification 
sudo systemctl status unattended-upgrades


#Test 

sudo unattened-upgrade --dry-run --debug

```

proof of installation : 

![](/images/unattended-upgrades(1).png)


## Intrusion Prevention System(IPS) 

fail2ban is a type of IPS system which we can easily configure and provide a layer of extra security on our system by constantly monitoring the system for any abnormal activities and block those risk. 


Installation Documentation (fail2ban)

``` bash 

#installs fail2ban. 
sudo apt install fail2ban -y


```

Configuration Documentation 

It is important for us to create a copy of main configuration file which for fail2ban is jail.conf . 

We can create a copy by using this command: 

```bash 

# this will copy jail.conf to jail.local 

sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

## NOTE : Please be careful of any typos.

```

After creating a copy we need to do some changes.

``` bash 

# use nano text editor to make changes in the local copy file we created. 

sudo nano /etc/fail2ban/jail.local


## navigate to [sshd] section and enable it. we can also tweak some settings like max retry . 

```

#### Image Example : 

![](/images/fail2ban(1).png)



### Verification of fail2ban

``` bash 
sudo fail2ban-client status sshd
```

![](/images/client-status-sshd.png)



## Scripting 


### Security-baseline

``` bash 

#!/bin/bash
# Security Baseline Verification Script
# Usage: sudo ./verify_security.sh

echo "=== Security Baseline Report: $(date) ==="
echo "Host: $(hostname) ($(hostname -I | awk '{print $1}'))"

# Colors
RED='\033[0;31m'
GREEN='\033[0;32m'
NC='\033[0m' # No Color

echo -e "\n[1] SSH Configuration"
# Check 1: Password Auth
if grep -q "^PasswordAuthentication no" /etc/ssh/sshd_config; then
    echo -e "Password Auth: ${GREEN}DISABLED${NC} (Secure)"
else
    echo -e "Password Auth: ${RED}ENABLED${NC} (Warning)"
fi

# Check 2: Root Login
if grep -q "^PermitRootLogin no" /etc/ssh/sshd_config; then
    echo -e "Root Login:    ${GREEN}DISABLED${NC} (Secure)"
else
    echo -e "Root Login:    ${RED}ENABLED${NC} (Warning)"
fi

echo -e "\n[2] Firewall (UFW)"
if command -v ufw &> /dev/null; then
    sudo ufw status | head -n 4
else
    echo -e "${RED}UFW not installed${NC}"
fi

echo -e "\n[3] Fail2Ban Status"
if systemctl is-active --quiet fail2ban; then
    echo -e "Service: ${GREEN}RUNNING${NC}"
    # Show only the jails currently active (cleaner output)
    sudo fail2ban-client status | grep "Jail list"
else
    echo -e "Service: ${RED}NOT RUNNING${NC}"
fi

echo -e "\n[4] AppArmor Status"
if command -v aa-status &> /dev/null; then
    # Simply count enforced profiles
    enforced_count=$(sudo aa-status --enforced | wc -l)
    echo -e "AppArmor: ${GREEN}ACTIVE${NC}"
    echo "Profiles Enforced: $enforced_count"
else
    echo -e "AppArmor: ${RED}NOT INSTALLED${NC}"
fi

echo -e "\n[5] Administrative Users (sudo group)"
getent group sudo | cut -d: -f4

echo -e "\n[6] Automatic Updates"
if systemctl is-enabled unattended-upgrades &> /dev/null; then
    echo -e "Auto-Updates: ${GREEN}ENABLED${NC}"
else
    echo -e "Auto-Updates: ${RED}DISABLED${NC}"
fi

echo -e "\n[7] System Resources"
free -h | grep "Mem"
df -h / | grep "/"

echo -e "\n=== Verification Complete ==="

```


## Monitoring Script

``` bash 
#!/bin/bash
# Simple Remote Server Monitor
# Usage: ./monitor.sh

SERVER="username@server_ip"
LOGFILE="monitor_$(date +%F).log"

echo "Collecting metrics from $SERVER..."

# Connect ONCE and run all commands
ssh -t $SERVER "
    echo '=== System Info ==='
    uname -a && uptime
    echo ''
    
    echo '=== CPU & Memory ==='
    free -h
    echo 'Top Processes:'
    ps aux --sort=-%cpu | head -6
    echo ''

    echo '=== Disk Space ==='
    df -h
    echo ''

    echo '=== Security: Failed Logins ==='
    # Uses sudo; might ask for password if not configured for passwordless
    sudo grep 'Failed password' /var/log/auth.log | tail -5
    echo ''
    
    echo '=== Fail2Ban Status ==='
    sudo fail2ban-client status
" | tee "$LOGFILE"

echo "Done! Report saved to $LOGFILE"

```

