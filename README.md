# de7ops-linux-training
de7ops-training

Here’s a detailed training document that covers your entire index while using a **WordPress deployment scenario** with **Apache, MySQL, and PHP** on a Linux server.

---

### **DevOps Training: Linux Essentials for WordPress Deployment**
#### **Objective**:
By the end of this training, the trainee should be able to:
- Set up a Linux server
- Install and configure required services for a WordPress website
- Troubleshoot and administer Linux systems efficiently
- Automate tasks using scripting

---

## **1. Package Installation**
### **Linux Families (Debian, Red Hat, SUSE)**
Linux distributions are broadly categorized into:
- **Debian-based** (Ubuntu, Debian) → Uses `apt`
- **Red Hat-based** (RHEL, CentOS, Fedora) → Uses `yum` or `dnf`
- **SUSE-based** (SLES, openSUSE) → Uses `zyper`

### **Installing Packages**
#### **Debian/Ubuntu**
```bash
sudo apt update
sudo apt install apache2 mysql-server php php-mysql -y
```

#### **RHEL/CentOS**
```bash
sudo yum update -y
sudo yum install httpd mariadb-server php php-mysql -y
```

#### **SUSE**
```bash
sudo zypper refresh
sudo zypper install apache2 mariadb php php-mysql -y
```

### **Checking Installed Packages**
```bash
dpkg -l | grep apache2    # Debian-based
rpm -qa | grep httpd      # RHEL-based
zypper se --installed-only apache2  # SUSE-based
```

---

## **2. Dependencies Installation & Repositories**
Some applications require additional dependencies.

### **Checking Repositories**
#### **Debian**
```bash
cat /etc/apt/sources.list
```

#### **RHEL**
```bash
yum repolist
```

#### **SUSE**
```bash
zypper lr
```

### **Installing Missing Dependencies**
```bash
sudo apt-get install -f   # Debian
sudo yum check            # RHEL
sudo zypper verify        # SUSE
```

---

## **3. Logs and Troubleshooting**
Logs help in debugging issues.

### **Common Log Files**
- **Apache Logs**:
  ```bash
  tail -f /var/log/apache2/access.log  # Debian
  tail -f /var/log/httpd/access_log    # RHEL
  ```
- **Error Logs**:
  ```bash
  tail -f /var/log/apache2/error.log   # Debian
  tail -f /var/log/httpd/error_log     # RHEL
  ```
- **System Logs**:
  ```bash
  tail -f /var/log/syslog  # Debian
  tail -f /var/log/messages # RHEL/SUSE
  ```
- **Journal Logs**:
  ```bash
  journalctl -xe
  ```

---

## **4. Network Connectivity**
### **Checking Connectivity**
```bash
ping google.com
```

### **Testing Reachability**
```bash
telnet example.com 80
```

### **Checking Port Listeners**
```bash
netstat -tulnp | grep LISTEN
```

### **DNS Resolution**
```bash
nslookup google.com
dig google.com
```

### **Checking Local Host Resolution**
```bash
cat /etc/hosts
```

### **Firewall Rules**
```bash
sudo ufw status         # Ubuntu
sudo firewall-cmd --list-all  # RHEL
sudo iptables -L -n -v  # Generic
```

---

## **5. Tunneling (Private Database Connection)**
If MySQL is only accessible privately, use SSH tunneling:
```bash
ssh -L 3307:localhost:3306 user@remote-server
mysql -h 127.0.0.1 -P 3307 -u root -p
```

---

## **6. File Transfer**
### **SCP (Secure Copy)**
```bash
scp file.txt user@remote-server:/home/user/
```

### **Rsync (Efficient Transfer)**
```bash
rsync -avz file.txt user@remote-server:/home/user/
```

### **FTP/SFTP**
```bash
ftp remote-server
sftp user@remote-server
```

---

## **7. Administration**
### **Disk Monitoring**
```bash
df -h
du -sh /var/www/html
```

### **Expanding Disk Space**
```bash
lvextend -L +5G /dev/mapper/root
resize2fs /dev/mapper/root
```

### **CPU/Memory Monitoring**
```bash
top
htop
free -m
```

### **Process Management**
```bash
ps aux | grep apache
kill -9 <PID>
systemctl restart apache2  # Debian
systemctl restart httpd    # RHEL
```

---

## **8. Checking Containers**
```bash
docker ps
docker logs <container_id>
docker restart <container_id>
```

---

## **9. User Management**
```bash
sudo useradd devops
sudo passwd devops
sudo usermod -aG sudo devops
sudo deluser devops
```

---

## **10. Archival & Compression**
```bash
tar -cvf backup.tar /var/www/html
gzip backup.tar
```

---

## **11. Scripting**
### **For Loop**
```bash
for i in {1..5}; do echo "Hello $i"; done
```

### **Grep**
```bash
grep "error" /var/log/apache2/error.log
```

### **if/else**
```bash
if [ -f /etc/passwd ]; then echo "File exists"; else echo "File missing"; fi
```

### **sed, awk, cut, sort, tail, head**
```bash
cat /etc/passwd | cut -d: -f1 | sort | head -5
```

### **Environment Variables**
```bash
export MY_VAR="Hello"
echo $MY_VAR
```

### **curl/wget**
```bash
curl -O https://wordpress.org/latest.tar.gz
wget https://wordpress.org/latest.tar.gz
```

---

## **12. Installing & Configuring WordPress**
```bash
cd /var/www/html
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
mv wordpress/* .
rm -rf wordpress latest.tar.gz
chown -R www-data:www-data .
chmod -R 755 .
```

### **Configuring Apache**
```bash
cat <<EOF | sudo tee /etc/apache2/sites-available/wordpress.conf
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html
    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
EOF
sudo a2ensite wordpress.conf
sudo systemctl restart apache2
```

### **Configuring MySQL**
```bash
sudo mysql -u root -p -e "
CREATE DATABASE wordpress;
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
FLUSH PRIVILEGES;"
```

---
