<h1 style="color:#6a00ff; background:linear-gradient(90deg,#6a00ff,#00d4ff); padding:12px; border-radius:10px; text-align:center;">
# Operating System Week 1.
</h1>

<h2 style="color:#ff006e; border-bottom:3px solid #ff006e; padding-bottom:6px;">
## Distribution Selection 
</h2>

<h3 style="color:#8338ec;">
### 1. Workstation : Pop!_OS
</h3>

<h3 style="color:#3a86ff;">
### 2. Server : Ubuntu server 
</h3>

<h2 style="color:#fb5607; border-bottom:3px solid #fb5607; padding-bottom:6px;">
## Distribution Selection Justification
</h2>

I have selected a Debain-based distribution. My primary motivation was to choose a platform that is very stable, reliable and beginner friendly. It uses apt package manager which make it easy for us to find and install almost any open-source software. Most of the open-source softwares are tested and released for apt first. The debain family is also backed by many documentation online which can be used if I get stuck during installation or any other system errors.

<h2 style="color:#ffbe0b; border-bottom:3px solid #ffbe0b; padding-bottom:6px;">
## Comparison with Other Alternatives
</h2>

There are also other alternatives which belongs to Red Hat , I did not choose this distribution because most of these OS are designed for enterprise level which is not free to use and it also uses different package manager called dnf which is not beginner friendly.

SUSE family was also in the option but it is also very widely used in enterprise-level server and it uses RPM package system which has less number of software support compared to apt. 

<h2 style="color:#00b4d8; border-bottom:3px solid #00b4d8; padding-bottom:6px;">
## System Architecure Diagram
</h2>

![](images/System_architecture_diagram.png)

NOTE : IP information about Windows host has not been displayed because of security reasons.

<h2 style="color:#52b788; border-bottom:3px solid #52b788; padding-bottom:6px;">
## Workstation Configuration 
</h2>

Workstation Choice : Pop!_OS with 8 GB RAM, 4 Cores.

Justification : I chose Pop!_OS because it is based on Ubuntu and it shares the same package manager as my ubuntu server which makes it easy to seamlessly interact with both OS. It is optimized for performance and  supports NVIDIA drivers out of the box. This configuration with 8GB RAM and 4 cores, is sufficient for development, gaming and multitasking, while remaining stable and user-friendly, and productive.

<h2 style="color:#38b000; border-bottom:3px solid #38b000; padding-bottom:6px;">
## Network Configuration Documentation
</h2>

**VirtualBox NAT Network Settings:**  
- Both **Pop!_OS** (workstation) and **Ubuntu Server** are connected to the same NAT Network named **r_network**.  
- Adapter 1 on both VMs is set to **NAT Network** with the "Cable connected" option enabled.  
- Using a NAT Network allows automatic IP assignment via VirtualBox DHCP while providing internet access for updates and package installation.

![pop os network settings in VM](images/popOS_network_settings.png)

![Ubuntu server network settings in VM](images/ubuntuserver_network_settings.png)

**IP Addressing:**  
- Both VMs receive IP addresses automatically from the **r_network DHCP server**.  
- Example IP assignments:

| VM            | IP Address       | Subnet Mask       | Gateway   |
|---------------|----------------|-----------------|---------------|
| Pop!_OS       | 10.0.2.15      | 255.255.255.0   | 10.0.2.1      |
| Ubuntu Server | 10.0.2.3       | 255.255.255.0   | 10.0.2.1      |

**Connectivity Verification:**  

<h2 style="color:#9d4edd;">
## IP addresses in both machines : 
</h2>

<h3 style="color:#5a189a;">
### Ubuntu server :
</h3>

![server ip ](images/server_ip.png)

<h2 style="color:#f72585;">
## Ping test to Desktop:
</h2>

![ping test from server](images/server_ping.png)

<h3 style="color:#4361ee;">
### Pop!OS : 
</h3>

![Pop!OS ip ](images/pop_os_ip.png)

<h2 style="color:#4cc9f0;">
## Ping test to Server: 
</h2>

![ping test from desktop ](images/popOSping.png)

<h2 style="color:#ff7b00; border-bottom:3px solid #ff7b00; padding-bottom:6px;">
## Linux command documentation (uname, lsb_release, free, df -h, ip addr )
</h2>

<h3 style="color:#e85d04;">
## Ubuntu : 
</h3>

![](images/ubuntu_command.png)

![](images/server_ip.png)

<h3 style="color:#00bbf9;">
## Pop!OS : 
</h3>

![](images/pop_os_documentation.png)

![](images/pop_os_ip.png)
