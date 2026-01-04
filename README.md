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

### 3.1 Create Backup Script

sudo mkdir -p /backups
sudo chmod 700 /backups

##Install Apache Server
sudo dnf install httpd -y
sudo vim /etc/httpd/conf/httpd.conf 
##Add Port to Listen port 8080 

vim /etc/httpd/conf.d/sarah.conf


**Script Content:**

```bash
#!/bin/bash
DATE=$(date +%F)
BACKUP_DIR="/backups"
LOG="/var/log/backup_integrity.log"

# Sarah - Apache Backup
tar -czf $BACKUP_DIR/apache_backup_$DATE.tar.gz /etc/httpd/ /var/www/html/ 2>/dev/null

# Mike - Nginx Backup
tar -czf $BACKUP_DIR/nginx_backup_$DATE.tar.gz /etc/nginx/ /usr/share/nginx/html/ 2>/dev/null

# Integrity Verification
echo "Verification for $DATE" >> $LOG
tar -tvf $BACKUP_DIR/apache_backup_$DATE.tar.gz > /dev/null && echo "Apache: OK" >> $LOG || echo "Apache: FAIL" >> $LOG
tar -tvf $BACKUP_DIR/nginx_backup_$DATE.tar.gz > /dev/null && echo "Nginx: OK" >> $LOG || echo "Nginx: FAIL" >> $LOG

```

*Permissions:* `sudo chmod +x /usr/local/bin/web_backup.sh`

### 3.2 Schedule Cron Job

Set to run every Tuesday at 12:00 AM.

```bash
sudo crontab -e

```

**Add this line:**

```cron
0 0 * * 2 /usr/local/bin/web_backup.sh

```

---

## Content for your DOCX Report

### Challenges Encountered

1. **Directory Permissions:** Initially, users could see each other's home folders. This was resolved by using `chmod 700`.
2. **Missing Repositories:** On CentOS 10, `htop` requires the `epel-release` repository to be enabled first.
3. **Cron Scheduling:** Ensuring the backup script had absolute paths for all commands to prevent failures during automated execution.

### Verification (Expected Screenshots)

* **Monitoring:** Screenshot of `htop` running.
* **Permissions:** Output of `ls -ld /home/sarah/workspace`.
* **Backups:** Output of `ls -lh /backups` showing the `.tar.gz` files.

---

## Final Steps for Submission

1. **Create Repository:** Go to GitHub and create a repo named `DevOps-Environment-Setup`.
2. **Upload Files:** Upload this `README.md` and the scripts.
3. **Submit Link:** Copy your GitHub URL and paste it into a Word document to upload to Vlearn.

**Would you like me to generate the exact text for the GitHub README file in a code block so you can copy it directly?**
