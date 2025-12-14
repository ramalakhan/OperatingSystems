# ASSESSMENT WEEK 6

# Performance Evaluation

## System at idle phase: 

### CPU 

![](/images/cpu(idle).png)

### Disk

![](/images/disk(idle).png)

### Network

![](/images/network(idle).png)

## Load testing 


load testing commands.

### CPU stress 
``` bash 
sysbench cpu --threads=2 run

```

### Memory Stress

``` bash 

stress-ng --vm 2 --vm-bytes 512M --timeout 30s

```

### Disk Stress

``` bash 

fio --name=test --rw=randwrite --bs=4k --size=256M

```
### Network Stress

``` bash

iperf3 -c 10.0.2.3  #from workstation to server . 

```

## 1. Performance Data Table : 

| Workload Scenario | CPU Usage (%) | Memory Usage | Disk Write Speed | Notes & Observations |
|------------------|---------------|--------------|------------------|---------------------|
| Baseline (Idle) | 0.7% | 246 MB | 0.00 MB/s | System idle. Minimal background processes active. |
| CPU Stress | 100% (2 Cores) | 275 MB | 0.00 MB/s | sysbench score: 1647.98 events/sec. CPU saturated. |
| Memory Stress | 100% | 2.92 GB | N/A | stress-ng pushed RAM usage to ~39%. No Swap usage observed (0K). |
| Disk Stress | 18.9% (sys) | 526 MB | 478 MB/s | High throughput (117k IOPS). fio random write test saturated the disk controller. |
| Network Stress | Not Tested | Not Tested | Not Tested | Pending execution of iPerf3 tests. |



## 2. Stress data visualization.

![](/images/bar_graph.png)



## Network Performance Testing

### ping test 


we will send 10 packets to our server machine. 
``` bash 

ping -c 10 10.0.2.3
# this will send 10 packets to our server machine.

```
![](/images/network(ping).png)


### Network Throughput testing via iperf3 

We will use iperf3 to perform network thoughput testing, before we eben begin to test our system we need to make some chnages in the firewall. 

NOTE : please make sure iperf3 is installed in both server and workstation .

### On server 

``` bash
iperf3 -s -p 6000

# this will open port 6000 for listening

```

### firewall configuraiton 

```bash 

# this will allow port 6000 to perform TCP connection with our system. 

sudo ufw allow 6000/tcp
sudo ufw allow 6000/udp


```


### On workstaion 

``` bash 

iperf3 -c 10.0.2.3 

#this tests TCP throughput by default. 

```

### Demonstration

![](/images/listener.png)


![](/images/testing.png)


## Network Performance Data Visualization. 

### Phase 6: Performance Data Summary

| Test Scenario | CPU Usage | Memory Usage | Disk Performance | Network Performance | Observations |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1. Baseline (Idle)** | **0.7%** | **246 MB** | 0.00 MB/s | 0.00 Mbps | System idle. Resources are minimal. |
| **2. CPU Stress** | **100%** (2 Cores) | 275 MB | 0.00 MB/s | N/A | Peak score: **1647.98 events/sec**. CPU successfully saturated. |
| **3. Memory Stress** | 100% | **2.92 GB** | N/A | N/A | RAM usage spiked to ~40%. **Zero Swap** usage (0K) indicates good RAM availability. |
| **4. Disk Stress** | ~99% (IO Wait) | 526 MB | **478 MB/s** | N/A | Throughput: **456 MiB/s**. High IOPS (117k). Disk is extremely fast (Likely SSD/NVMe). |
| **5. Network Stress** | N/A | N/A | N/A | **289 Mbits/sec** | **Avg Latency: 2.03ms**. High Retransmissions (**537**) indicate a TCP buffer bottleneck. |

## 2. Bottleneck Identification & Analysis

After running stress tests on all major system resources, we analysed the data to find which part of the system is slowing down performance.

###  Primary Bottleneck: The Network
* **Status:** **CRITICAL**
* **Observation:** The network speed reached only **289 Mbps** (far below the expected 1000 Mbps).
* **The Evidence:** The iPerf3 test showed **537 Retransmissions**.
* **Why this matters:** A "retransmission" means data packets were dropped or lost and had to be sent again. This indicates the server's default network settings (TCP buffers) are too small to handle high-speed traffic, causing a "traffic jam."
* **Action Required:** We need to tune the network stack to increase buffer sizes.

###  Secondary Constraint: The CPU
* **Status:** **SATURATED (Normal)**
* **Observation:** The CPU hit **100% usage** across all cores.
* **The Evidence:** `sysbench` showed a constant 100% load with **1647 events per second**.
* **Why this matters:** The CPU is doing exactly what it should—working at maximum capacity. While it is a limit, it is not "misbehaving." It is simply the hardware limit of the Virtual Machine.

###  Healthy Resources: Disk & Memory
* **Disk Storage:** **EXCELLENT**.
    * The disk wrote data at **478 MB/s** with high responsiveness (117k IOPS). This is faster than most standard SSDs and is definitely **not** a bottleneck.
* **Memory (RAM):** **HEALTHY**.
    * Even under heavy load, the system **did not use Swap** (Swap usage: 0K). This means the system has enough physical RAM to handle applications without slowing down.

### Conclusion
The system has fast storage and enough RAM, but the **Network Configuration is unoptimised**, leading to lost data packets. Phase 6 optimisation will focus on fixing these network buffers.

## 3. Optimisation & Re-Testing

To improve performance, I implemented two changes based on the resource management techniques explored in Lab Week 11.

### Improvement 1: Cache Management
* **Method:** Used the kernel command from Lab Task 2.2 (`echo 3 > /proc/sys/vm/drop_caches`) to clear the page cache.
* **Result:** This immediately freed up RAM that was "buffering" old files, making more physical memory available for the stress test application.

### Improvement 2: Memory Tuning (Swappiness)
* **Method:** Reduced `vm.swappiness` from 60 to 10.
* **Reasoning:** The default setting causes the system to use the disk (swap) too early. By lowering this value, I forced the system to utilize its faster physical RAM.
* **Observation:** The re-test showed the system handling the 1GB memory load more efficiently without unnecessary disk I/O.
