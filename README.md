Multi-Node RHEL 9 Infrastructure Monitoring Stack
A professional monitoring and observability solution for Linux environments. This project demonstrates the deployment of a Prometheus and Grafana stack to monitor system health across multiple RHEL 9 nodes.

📋 Table of Contents
1. **Overview**
2. **Architecture**
3. **Tech Stack**
4. **Implementation Details**
5. **Challenges & Troubleshooting (The "RHEL Factor")**
6. **Final Results**

**🔍 Overview**
The goal of this project was to establish a centralized monitoring system for a cluster of 4 RHEL 9 Virtual Machines. It provides real-time visibility into CPU usage, Memory consumption, Disk I/O, and the status of critical system services (like sshd, httpd, php, mariadb).

**🏗️ Architecture**
1. **Control Node (192.168.75.138):** Runs Prometheus and Grafana.
2. **Managed Nodes (3 VMs):** Running Node Exporter to feed data to the Control Node.
3. **Network**: VMware Workstation 17 Pro NAT Network.
4. **Host**: Windows 11 (Accessing the web UI).

**🛠️ Tech Stack**
1. **OS**: Red Hat Enterprise Linux (RHEL) 9.3
2. **Monitoring**: Prometheus (Data Collection)
3. **Visualization**: Grafana (Dashboards)
4. **Data Source**: Node Exporter (Hardware Metrics)
5. **Security**: Firewalld, SELinux

**🚀 Implementation Details**
1. Agent Deployment (Node Exporter)
On all nodes, Node Exporter was installed as a systemd service.
   a. Binary Path: /usr/local/bin/node_exporter
   b.Port: 9100

2. Monitoring Setup (Prometheus)
Configured prometheus.yml to scrape metrics from all 3 VMs.

<img width="606" height="162" alt="image" src="https://github.com/user-attachments/assets/9d5ed060-536e-4b26-bbbb-8ab126c387de" />

3. Dashboard Configuration (Grafana)
a. Integrated Prometheus as the primary data source.
b. Imported Dashboard ID 1860 for comprehensive hardware visualization.
c. Created custom Service Status panels using PromQL to track service uptime.

**🛡️ Challenges & Troubleshooting (The "RHEL Factor")**
Deploying on RHEL 9 presented unique security challenges that required advanced Linux administration:

1. SELinux "Permission Denied" (203/EXEC)
   
Custom binaries in /usr/local/bin were initially blocked by SELinux.
Solution: Reset the security context to allow execution.

Commands used: 
 sudo chcon -t bin_t /usr/local/bin/node_exporter

 sudo restorecon -v /usr/local/bin/node_exporter

2. Firewalld Port Management
   
Managed nodes refused connections on port 9100.
Solution: Configured persistent firewall rules for the specific monitoring ports.

Commands used: 
 sudo firewall-cmd --permanent --add-port=9100/tcp

 sudo firewall-cmd --reload

3. Grafana Internal Network Blocking
Grafana was unable to "dial" the local Prometheus API due to SELinux socket restrictions.
Solution: Enabled the network connection boolean.

Command used: 
 sudo setsebool -P nis_enabled 1

4. SELinux Policy Troubleshooting
During the integration of Grafana and Prometheus, I encountered a permission denied error when Grafana attempted to query the Prometheus API on localhost:9090
RHEL 9's strict SELinux policy prevents the Grafana process from initiating outbound network connections by default.

The Solution:
Initially, I attempted to use a Grafana-specific boolean, but discovered it was not defined in the standard RHEL 9 policy. I successfully pivoted to the httpd network boolean, which grants the necessary socket permissions for web-based services to communicate internally:

Command used: 
**# Attempted (Result: Boolean not defined)**
sudo setsebool -P grafana_can_network_connect 1

**# Successful Pivot (Standard RHEL 9 Workaround)**
sudo setsebool -P httpd_can_network_connect 1


**📊 Final Results**

Prometheus Targets: 

<img width="947" height="392" alt="image" src="https://github.com/user-attachments/assets/a6f1bbc0-d61d-471f-a8a9-e9f2a7cf1adf" />


System Dashboard: 
<img width="949" height="438" alt="image" src="https://github.com/user-attachments/assets/0d1a62dd-dcd9-4df4-a531-13a7f90d6aeb" />

<img width="949" height="438" alt="image" src="https://github.com/user-attachments/assets/8c8e3470-961c-4b4f-bb5d-a0f6c72ccf34" />

<img width="941" height="325" alt="image" src="https://github.com/user-attachments/assets/9e278ae8-46dc-476e-a46c-c004eaa338fa" />

**👨‍💻 Key Skills Demonstrated:**
1. Linux Hardening: Managing SELinux policies and Firewalld zones.
2. Infrastructure as Code (Mental): Organizing multi-node service deployments.
3. Observability: Building meaningful visualizations from raw time-series data.
4. Troubleshooting: Diagnosing 203/EXEC errors and network socket issues.

LOG SNIPPETS:

<img width="758" height="245" alt="image" src="https://github.com/user-attachments/assets/e7b0d2a2-45c0-41bc-9add-dfd1f8272009" />

<img width="757" height="183" alt="image" src="https://github.com/user-attachments/assets/ec619fe9-ff24-4e14-8f37-fbe897fb9228" />




