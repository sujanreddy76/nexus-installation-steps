
````markdown
# Manual Nexus Installation Guide

This guide walks you through the manual installation and setup of Nexus Repository Manager on a Linux system.

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

## Create Nexus as a Service

```bash
ln -s /opt/nexus/bin/nexus /etc/init.d/nexus
```

---

## Start Nexus Service

Switch to nexus user:

```bash
sudo su - nexus
```

Enable and start Nexus:

```bash
sudo systemctl enable nexus
sudo systemctl start nexus
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

## Notes

* Default Nexus URL: `http://<your-server-ip>:8081`
* Initial admin password is stored in:

```bash
/opt/sonatype-work/nexus3/admin.password
```

- ⚙️ Ansible playbook (since you were working on that)
```
