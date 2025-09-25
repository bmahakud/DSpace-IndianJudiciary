 🚀 DSpace Installation Guide (Ubuntu 22.04 LTS)

This guide provides step-by-step instructions to install **DSpace** on Ubuntu 22.04 LTS with the required dependencies.

---

## 📂 Directory Hierarchy

We will create a structured directory layout for the installation:

```bash
mkdir dspace
cd dspace
mkdir root     # Compiled files will go here
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

Remove old versions if any:

```bash
sudo apt remove maven -y
```

Download and install latest Maven:

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

Install PostgreSQL (12, 13 or 14):

```bash
sudo apt install -y postgresql postgresql-contrib
```

Switch to postgres user:

```bash
sudo -i -u postgres psql
```

Run the following inside psql:

```sql
CREATE DATABASE dspace;
CREATE USER dspace WITH PASSWORD 'dspace';
ALTER DATABASE dspace OWNER TO dspace;
GRANT ALL PRIVILEGES ON DATABASE dspace TO dspace;

\c dspace
CREATE EXTENSION pgcrypto;

\q
```

---

## ⚙️ Install Servers (Tomcat + Solr)

Go to servers directory:

```bash
cd ~/dspace/servers
```

### Download and extract Tomcat 10.x

```bash
wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.36/bin/apache-tomcat-10.1.36.tar.gz
tar -xvzf apache-tomcat-10.1.36.tar.gz
```

### Download and extract Solr 8.11.x

```bash
wget https://www.apache.org/dyn/closer.lua/lucene/solr/8.11.4/solr-8.11.4.tgz?action=download -O solr-8.11.4.tgz
tar -xvzf solr-8.11.4.tgz
```

Cleanup:

```bash
rm -rf solr-8.11.4.tgz apache-tomcat-10.1.36.tar.gz
```

---

## 📦 Install DSpace (Backend + Frontend)

Go to source folder:

```bash
cd ~/dspace/source
```

Clone repositories:

```bash
git clone https://github.com/DSpace/DSpace.git backend
git clone https://github.com/DSpace/dspace-angular.git frontend
```

### Configure Backend

```bash
cp backend/dspace/config/local.cfg.EXAMPLE backend/dspace/config/local.cfg
```

Edit `local.cfg` and set your DSpace directory path (replace with your actual path):

```properties
dspace.dir=/home/<username>/dspace/root
```

---

## 🎯 Next Steps

* Compile backend:

  ```bash
  cd backend
  mvn package -DskipTests
  ```

* Deploy backend to Tomcat (`webapps` directory).

* Build Angular frontend:

  ```bash
  cd frontend
  npm install
  npm run build
  ```

* Configure Solr cores using files from DSpace source.

---

## ✅ Summary

You now have:

* **Java 17** installed
* **Maven 3.9.6** configured
* **PostgreSQL** database ready
* **Tomcat 10** and **Solr 8.11.4** extracted
* **DSpace backend + frontend** cloned and configured


