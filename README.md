

* Directory structure
* Prerequisites
* Database setup (with **dump restore**, migrations, privileges)
* Backend + frontend setup
* Tomcat + Solr installation
* Indexing and registry loading

Here’s the **full documentation** you can use:

---

 📘 DSpace Installation & Setup Guide

## 📂 Directory Layout

```bash
mkdir ~/dspace
cd ~/dspace
mkdir root     # Compiled DSpace files will go here
mkdir servers  # Tomcat and Solr installation
mkdir source   # DSpace frontend and backend source code
```

---

## ✅ Prerequisites

### 1. Update System

```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install Required Tools

```bash
sudo apt install -y git unzip wget curl ant
```

### 3. Install Java 17

```bash
sudo apt install -y openjdk-17-jdk
```

### 4. Install Maven (3.8.x or above)

Remove old versions:

```bash
sudo apt remove maven -y
```

Download & install latest Maven:

```bash
wget https://dlcdn.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.tar.gz
tar -xvzf apache-maven-3.9.6-bin.tar.gz
sudo mv apache-maven-3.9.6 /opt/maven
```

Add to `~/.bashrc`:

```bash
export M2_HOME=/opt/maven
export PATH=$M2_HOME/bin:$PATH
```

Reload:

```bash
source ~/.bashrc
```

Verify:

```bash
mvn -version
```

---

## 🗄️ Database Setup (PostgreSQL)

### 1. Install PostgreSQL

```bash
sudo apt install -y postgresql postgresql-contrib
```

Perfect! Since your `dspace_db.dump` file is already in the repo, we can write clear documentation so anyone can restore it and inspect the database tables. Here's a ready-to-use snippet you can put in your README or separate docs:

---

## Restore DSpace Database from Dump

This guide explains how to restore the `dspace_db.dump` file included in this repository and view the tables.

### 1. Prerequisites

* PostgreSQL installed on your system.
* User `dspace` and database `dspace` created. If not, run:

```bash
sudo -i -u postgres psql
```

Inside `psql`:

```sql
CREATE DATABASE dspace;
CREATE USER dspace WITH PASSWORD 'dspace';
ALTER DATABASE dspace OWNER TO dspace;
GRANT ALL PRIVILEGES ON DATABASE dspace TO dspace;
\q
```

---

### 2. Restore the Database

Navigate to the folder containing the dump:

```bash
cd ~/DiracAI/dspace
```

#### Option 1: Using `pg_restore` (if dump is custom format)

```bash
pg_restore -U dspace -d dspace dspace_db.dump
```
If got **Authentication failed**

```bash
sudo nano /etc/postgresql/14/main/pg_hba.conf
```
Under **# Database administrative login by Unix domain socket**
edit or add:
```
local   all             all                                     md5
```

After adding restart the postgresql:
```bash
sudo service postgresql restart
```

#### Option 2: Using `psql` (if dump is SQL format)

```bash
psql -U dspace -d dspace -f dspace_db.dump
```

> Enter the password `dspace` when prompted.

---

### 3. Verify the Tables

Connect to the restored database:

```bash
sudo -i -u postgres psql -d dspace
```

List all tables:

```sql
\dt
```

You should see a list of DSpace tables like `item`, `bitstream`, `collection`, etc.

Exit `psql`:

```sql
\q
```

---

✅ **Now the database is ready for use** with the DSpace backend.

## ⚙️ Install Servers (Tomcat + Solr)

Go to servers directory:

```bash
cd ~/dspace/servers
```

### 1. Install Tomcat 10.x

```bash
wget https://archive.apache.org/dist/tomcat/tomcat-10/v10.1.36/bin/apache-tomcat-10.1.36.tar.gz
tar -xvzf apache-tomcat-10.1.36.tar.gz
```

### 2. Install Solr 8.11.x

```bash
wget https://www.apache.org/dyn/closer.lua/lucene/solr/8.11.4/solr-8.11.4.tgz?action=download -O solr-8.11.4.tgz
tar -xvzf solr-8.11.4.tgz
```

### 3. Cleanup

```bash
rm -rf solr-8.11.4.tgz apache-tomcat-10.1.36.tar.gz
```

---

## 📦 Install DSpace (Backend + Frontend)

Go to source folder:

```bash
cd ~/dspace/source
```

### 1. Clone Repositories

```bash
git clone https://github.com/bmahakud/DSpace-IndianJudiciary-FrontEnd frontend
git clone https://github.com/bmahakud/DSpace-IndianJudiciary-BackEnd backend
```

### 2. Configure Backend

```bash
cp backend/dspace/config/local.cfg.EXAMPLE backend/dspace/config/local.cfg
```

Edit `local.cfg` and set your DSpace directory:

Get the path of root(go to dspace/root):

```bash
pwd
```

```properties
dspace.dir= /diracai/dspace/root
```

### 3. Compile Backend

```bash
cd backend
mvn clean package
```

Deploy backend `.war` file to Tomcat `webapps/`.

### 4. Build Angular Frontend

```bash
cd frontend
npm install
npm run build
```

---

## 🔄 Database Migration & Indexing

Go to DSpace **bin directory** (`~/dspace/root/bin`):

```bash
# Rebuild Discovery index
sudo ./dspace index-discovery -b

# Load metadata registry
sudo ./dspace registry-loader -metadata ../config/registries/dublin-core-types.xml

# Run database migrations
sudo ./dspace database migrate

# Create administrator account
sudo ./dspace create-administrator
```

---

## 🎯 Summary

At this stage, you have:

* ✅ Java 17 installed
* ✅ Maven 3.9.6 configured
* ✅ PostgreSQL database ready with dump restored
* ✅ Tomcat 10 and Solr 8.11.4 installed
* ✅ DSpace backend + frontend cloned and configured
* ✅ Database migrations + indexing done
* ✅ Administrator account created

Now you can start Tomcat and access your DSpace instance
