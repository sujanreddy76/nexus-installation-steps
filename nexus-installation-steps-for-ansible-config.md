# Nexus Installation Guide For Ansible

This guide provides step-by-step instructions to install and configure Nexus Repository Manager on a Linux system, designed to align with automation using Ansible.


---

## Prerequisites

Nexus is built on Java, so Java must be installed before running Nexus.

### Install Required Packages

```bash
apt update
apt install openjdk-8-jre-headless -y
apt install net-tools -y
````

* `openjdk-8-jre-headless` → Required to run Nexus
* `net-tools` → Useful for debugging (checking ports)

---

## Download and Extract Nexus

```bash
# Download Nexus (you can check for latest version from Sonatype website)
wget https://download.sonatype.com/nexus/3/nexus-3.45.0-01-unix.tar.gz

# Extract the archive
tar -xzvf nexus-3.45.0-01-unix.tar.gz

# Move Nexus to /opt directory
mv nexus-3.45.0-01 /opt/nexus
```

---

## Create Nexus User

> Running Nexus as root is not recommended.

```bash
useradd nexus
```

### Grant Sudo Access

```bash
visudo
```

Add the following line:

```bash
nexus ALL=(ALL) NOPASSWD: ALL
```

---

## Set Permissions

```bash
# Set ownership and permissions for Nexus directory
chown -R nexus:nexus /opt/nexus
chmod -R 775 /opt/nexus

# Set ownership and permissions for sonatype-work directory
chown -R nexus:nexus /opt/sonatype-work
chmod -R 775 /opt/sonatype-work
```

---

## Configure Nexus to Run as Nexus User

Edit the Nexus configuration file:

```bash
vi /opt/nexus/bin/nexus.rc
```

Uncomment and update:

```bash
run_as_user="nexus"
```

---

## Configure Nexus as a Systemd Service

Create a systemd service file:

```bash
vi /etc/systemd/system/nexus.service
```

Add the following content:

```bash
[Unit]
Description=Nexus Repository Manager
After=network.target

[Service]
Type=forking
LimitNOFILE=65536
User=nexus
Group=nexus
ExecStart=/opt/nexus/bin/nexus start
ExecStop=/opt/nexus/bin/nexus stop
Restart=on-abort

[Install]
WantedBy=multi-user.target
```

Reload systemd and enable Nexus:

```bash
systemctl daemon-reload
systemctl enable nexus
systemctl start nexus
```

---

## (Alternative) Create Nexus as Init Service

```bash
ln -s /opt/nexus/bin/nexus /etc/init.d/nexus
```

---

## Verify Nexus is Running

```bash
ps aux | grep nexus
netstat -lnpt
```

* Check if Nexus process is running
* Confirm it is listening on the default port (usually 8081)

---

## Nexus Login and Configuration

### Login to Nexus

* Default admin username: `admin`
* Admin password is located in:

```bash
/opt/sonatype-work/nexus3/admin.password
```

---

## Change Nexus Port

```bash
vim /opt/nexus/etc/nexus-default.properties
```

* Modify the port as needed

Restart Nexus:

```bash
service nexus restart
```

---

## Notes

* Default Nexus URL: `http://<your-server-ip>:8081`

```

