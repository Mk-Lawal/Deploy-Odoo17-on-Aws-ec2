

# Deploying Odoo17 ERP on AWS EC2

## Overview

Odoo is a fully integrated and customizable resource planning system, open-source business management software. It integrates various business applications including CRM, sales, project management, manufacturing, inventory management, accounting, HR management, marketing, and customer support tools into one solution. This guide outlines the steps to deploy and configure Odoo17 ERP on an AWS EC2 instance for monitoring.

![Odoo17 Diagram](https://github.com/Mk-Lawal/Deploy-Odoo17-on-Aws-ec2/blob/ce475303af466179fe18e2220401c3c7e88ae9b3/Odoo17.png)

## Prerequisites

- An AWS EC2 instance running Ubuntu 20.04 or later
- Basic knowledge of Linux commands and AWS management
- SSH access to the EC2 instance

## Steps to Deploy and Configure Odoo17

### 1. Connect to Server and Upgrade the Environment

```bash
sudo apt-get update
sudo apt-get upgrade
```

### 2. Install Backend Dependencies

```bash
sudo apt-get install -y python3-pip
sudo apt-get install python3-cffi python3-dev libxml2-dev libxslt1-dev zlib1g-dev libsasl2-dev libldap2-dev build-essential libssl-dev libffi-dev libmysqlclient-dev libjpeg-dev libpq-dev libjpeg8-dev liblcms2-dev libblas-dev libatlas-base-dev
sudo apt-get install openssh-server fail2ban
```

### 3. Install Frontend Dependencies

```bash
sudo apt-get install -y npm
sudo ln -s /usr/bin/nodejs /usr/bin/node
sudo npm install -g less less-plugin-clean-css
sudo apt-get install -y node-less
```

### 4. Install and Configure PostgreSQL

```bash
sudo apt-get install postgresql
sudo su - postgres
createuser --createdb --username postgres --no-createrole --no-superuser --pwprompt odoo17
psql
ALTER USER odoo17 WITH SUPERUSER;
\q
exit
```

### 5. Add System User for Odoo17

```bash
sudo adduser --system --home=/opt/odoo17 --group odoo17
```

### 6. Install Git and Odoo17

```bash
sudo apt-get install git
sudo su - odoo17 -s /bin/bash
git clone https://www.github.com/odoo/odoo --depth 1 --branch 17.0 --single-branch .
exit
```

### 7. Install Python Dependencies

```bash
sudo pip3 install -r /opt/odoo17/requirements.txt
```

### 8. Install Additional Packages

```bash
wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.0g-2ubuntu4_amd64.deb
sudo dpkg -i libssl1.1_1.1.0g-2ubuntu4_amd64.deb
sudo wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.bionic_amd64.deb
sudo dpkg -i wkhtmltox_0.12.5-1.bionic_amd64.deb
sudo apt install -f
```

### 9. Setup Odoo Configuration and Service Files

```bash
sudo cp /opt/odoo17/debian/odoo.conf /etc/odoo17.conf
sudo nano /etc/odoo17.conf
```

Edit the configuration file to include:

```ini
[options]
   ; This is the password that allows database operations:
   admin_passwd = admin
   db_host = False
   db_port = False
   db_user = odoo17
   db_password = #Qt$67KN
   addons_path = /opt/odoo17/addons
   logfile = /var/log/odoo/odoo17.log
```

```bash
sudo chown odoo17: /etc/odoo17.conf
sudo chmod 640 /etc/odoo17.conf

sudo mkdir /var/log/odoo
sudo chown odoo17:root /var/log/odoo

sudo nano /etc/systemd/system/odoo17.service
```

Add the following content to the service file:

```ini
[Unit]
   Description=Odoo17
   Documentation=http://www.odoo.com

[Service]
   # Ubuntu/Debian convention:
   Type=simple
   User=odoo17
   ExecStart=/opt/odoo17/odoo-bin -c /etc/odoo17.conf

[Install]
   WantedBy=default.target
```

```bash
sudo chmod 755 /etc/systemd/system/odoo17.service
sudo chown root: /etc/systemd/system/odoo17.service
```

### 10. Start Odoo17 Service

```bash
sudo systemctl start odoo17.service
```

## Conclusion

Odoo17 is now deployed and running on your AWS EC2 instance. You can access it by navigating to the public IP of your EC2 instance in a web browser. 

For more details on configuration or troubleshooting, refer to the [Odoo documentation](https://www.odoo.com/documentation/17.0/).

