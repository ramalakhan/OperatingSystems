# ASSESSMENT WEEK 3


## INTRODUCTION 

In this week we will select application and linux tools to monitor our system. We will also develop a very simple strategy to effectively detect and observe performance difference. Furthermore, we will also use command to remotely install those tools using ssh. 


## Application Selection matrix and justification.

### Application Selection Matrix

| Workload Type | Selected Application | Description | Justification for Selection |
| :--- | :--- | :--- | :--- |
| **CPU-Intensive** | **Sysbench** (CPU) | A standard multi-threaded benchmark tool used to verify processor performance. | **Pure Computation:** It isolates the CPU by calculating prime numbers. This provides a clean baseline to measure how security overhead (like background monitoring processes) impacts raw processing power. |
| **RAM-Intensive** | **Stress-ng** | A tool designed to stress various kernel interfaces and memory subsystems. | **Memory Thrashing:** It allows exact control over memory allocation. This is essential for testing stability under pressure and verifying how the system behaves when it runs out of physical RAM and must use swap space. |
| **I/O-Intensive** | **Fio** (Flexible I/O) | An advanced I/O benchmark tool that simulates specific disk usage patterns. | **Disk Latency vs. Speed:** It separates random read/write speeds from sequential ones. This is critical for measuring the performance penalty introduced by file system encryption or logging services. |
| **Network-Intensive** | **iPerf3** | A tool for active measurements of the maximum achievable bandwidth on IP networks. | **Throughput Isolation:** It tests pure network speed (TCP/UDP) between two points without disk interference. This is necessary to verify firewall throughput and ensure network security rules aren't throttling legitimate traffic. |



## Installation Documentation With command for ssh Based Installation.

``` bash 

sudo apt install stress-ng -y   #installs stress ng 
stress-ng --version     # verifies installation 


##fio (Io intensive tool )

sudo apt install fio -y # installs fio tool 
fio --version            # verifies installation


## iperf3 installation 

sudo apt install iperf3 -y          #installs iperf3
iperf3 --version                    # Verifies installation


```

NOTE : The package manager of ubuntu is apt which is why we use apt install application_name but on other linux family the package manager could be dnf/ pacman etc.


## SSH Based installation

we can remotely installs these tools into our server using ssh. 

The command follows a very simple structure : 

ssh -t ram@10.0.2.3 "command to install tool"


NOTE : we use -t to open a terminal which allows us to input our password for successful ssh. 

The command to install tool has already been mentioned above. 

### Demonstration of installation and verification of tools installed. 

![](/images/stress-ng(installation).png)


Verification: 

![](/images/stress-ng(verification).png)


Using the similar pattern we will install all tools and verify their installation. 

![](/images/iperf3(2).png)



## Anticipated Resource Profiles 

### Expected Resource Profiles

| Application | Primary Resource Target | Expected Resource Utilization | Key Metrics to Monitor |
| :--- | :--- | :--- | :--- |
| **Sysbench** (CPU) | Processor (CPU) | **~100% Utilization:** Immediate spike to maximum load on all assigned cores. | • `%User` CPU Usage <br> • Load Average |
| **Stress-ng** (RAM) | Memory (RAM) & Swap | **Saturation:** Fills physical RAM to limit; triggers heavy Swap partition usage. | • `Free` vs `Available` Memory <br> • Swap Usage % |
| **Fio** (I/O) | Disk Subsystem | **Throughput Limit:** Hits maximum Read/Write MB/s and high I/O Wait times. | • Read/Write MB/s <br> • `%iowait` CPU State |
| **iPerf3** (Network) | Network Interface | **Bandwidth Cap:** Reaches maximum link speed (e.g., 1Gbps) with zero disk usage. | • Throughput (Mbits/sec) <br> • Packet Loss % |


## Monitoring Strategy with Measurement Approach. 

### Monitoring Strategy 


### Strategy 1
Observe the machine idle state and look for key metrics such as CPU usage , IO usage, Memory Usage and so on. 

Use monitoring tools such as : 

1. top 
2. htop   (upgraded version of top)
3. nmon   ( user friendly and all in one monitoring tool)
4. btop    (beautiful-top, a better GUI version of top)

### Strategy 2

Use workstation for ssh and open multiple terminals with ssh connections to the remote server and use one termial to generate workloads while other to monitor the performance using another termial. We can also compare the monitoring tools to find out which tools give us more accurate data about the system. 





