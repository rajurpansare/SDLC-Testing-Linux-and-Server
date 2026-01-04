# SDLC-Testing-Linux-and-Server

## Task 1: System Monitoring Setup

**Objective:** Monitor health and performance for Sarah and Mike’s environment.

### 1.1 Install Monitoring Tools

CentOS 10 uses `dnf`. We will install `htop` for real-time monitoring.

```bash
sudo dnf install -y epel-release
sudo dnf install -y htop nmon

```

### 1.2 Disk and Process Monitoring

We will create a monitoring script to log resource usage.

```bash
# Create a monitoring script
sudo nano /usr/local/bin/sys_monitor.sh

```

**Script Content:**

#!/bin/bash
LOG_FILE="/var/log/system_health.log"
echo "--- Report Date: $(date) ---" >> $LOG_FILE
echo "Disk Usage (df -h):" >> $LOG_FILE
df -h >> $LOG_FILE
echo "Top 5 Resource Intensive Processes:" >> $LOG_FILE
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu | head -n 6 >> $LOG_FILE
echo "--------------------------" >> $LOG_FILE

*Permissions:* `sudo chmod +x /usr/local/bin/sys_monitor.sh`

Add entry in Crontab file
#System Log Monitor backup
0 * * * * /usr/local/bin/sys_monitor.sh

---

## Task 2: User Management and Access Control

**Objective:** Securely onboard Sarah and Mike.

### 2.1 Create Users and Isolated Directories

# Create users
sudo useradd sarah
sudo useradd mike

# Create workspaces
sudo mkdir -p /home/sarah/workspace
sudo mkdir -p /home/mike/workspace

# Set ownership and strict permissions (700 = only owner can read/write/exec)
sudo chown sarah:sarah /home/sarah/workspace
sudo chmod 700 /home/sarah/workspace

sudo chown mike:mike /home/mike/workspace
sudo chmod 700 /home/mike/workspace


### 2.2 Password Policy

To enforce a 30-day expiration and complexity:

# Set expiration
sudo chage -M 30 sarah
sudo chage -M 30 mike

# Complexity: Edit /etc/security/pwquality.conf
# Ensure these lines exist:
# minlen = 12
# dcredit = -1
# ucredit = -1
# lcredit = -1
# ocredit = -1

/etc/security/pwquality.conf

<img width="628" height="604" alt="Screenshot 2026-01-05 at 12 30 40 AM" src="https://github.com/user-attachments/assets/33f9892f-8b5f-4a33-8189-c1fadf51a080" />


---

## Task 3: Backup Configuration

**Objective:** Automate Apache and Nginx backups.

### 3.1 Create Backup Directory

sudo mkdir -p /backups
sudo chmod 700 /backups

### 3.2 Installtion of Apache and Nginx Sever
### 3.2.1 Install Apache Server
sudo dnf install httpd -y
sudo vim /etc/httpd/conf/httpd.conf 
##Add Port to Listen port 8080 

<img width="611" height="305" alt="Screenshot 2026-01-04 at 8 39 47 PM" src="https://github.com/user-attachments/assets/a5ae5a79-35ca-4a9e-a9d3-57344b7d7d89" />


vim /etc/httpd/conf.d/sarah.conf
<img width="555" height="191" alt="Screenshot 2026-01-04 at 8 34 19 PM" src="https://github.com/user-attachments/assets/b1052d28-ce88-4b02-a0a7-afaa4a0944ea" />

### 3.2.2 Install Nginx Server
sudo dnf install nginx -y
Enable Listen port 80 for Nginx
sudo vim /etc/nginx/conf.d/mike.cof

<img width="546" height="212" alt="Screenshot 2026-01-05 at 12 51 19 AM" src="https://github.com/user-attachments/assets/bcc245cb-1f1f-45f1-853d-ae3eeb0a6e4e" />

### 3.3 --Parmanently enabled port in Firewall for Apache and Nginx

## Apache port 8080
## Nginx port 80
sudo firewall-cmd --permanent --add -port=80/tcp
sudo firewall-cmd --permanent --add -port=8080/tcp

sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https

<img width="537" height="78" alt="Screenshot 2026-01-04 at 9 01 36 PM" src="https://github.com/user-attachments/assets/3155c9b1-cae3-470c-8d26-27e21855614d" />

### 3.4 Create Script for Backup
## 3.4.1 Creating Script for Apache server
 sudo vim /usr/local/bin/backup_apache.sh

**Script Content:**

<img width="501" height="263" alt="Screenshot 2026-01-05 at 1 05 11 AM" src="https://github.com/user-attachments/assets/c4f14154-d6e0-4c64-8455-a49ef86b6f7b" />

## 3.4.2 Creating Script for Nginx server
sudo vim /usr/local/bin/backup_nginx.sh

**Script Content:**

<img width="497" height="264" alt="Screenshot 2026-01-05 at 1 08 13 AM" src="https://github.com/user-attachments/assets/fc82634f-bc2e-4991-a42c-248bf2c43cf5" />


## 3.5 *Permissions to Backup Scripts:* 
sudo chmod +x /usr/local/bin/backup_apache.sh
sudo chmod +x /usr/local/bin/backup_nginx.sh

### 3.6 Schedule Cron Job

Set to run every Tuesday at 12:00 AM.

sudo crontab -e


**Add this line:**


0 0 * * 2 /usr/local/bin/backup_apache.sh
0 0 * * 2 /usr/local/bin/backup_nginx.sh

<img width="354" height="123" alt="Screenshot 2026-01-05 at 1 14 03 AM" src="https://github.com/user-attachments/assets/bb3a79b1-7bbf-450d-a3d2-4eed39bc3567" />


---
