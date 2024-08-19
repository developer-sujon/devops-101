# MongoDB Installation and Replica Set Setup

## Introduction

This guide provides instructions for installing MongoDB, creating a user, and configuring a replica set. It covers both standalone and configuration file-based deployments.

## Prerequisites

- **Ubuntu Server** (20.04 or later recommended)
- **SSH Access** to the server
- Basic knowledge of Linux command-line operations

## Step 1: Install MongoDB

1. **Import the MongoDB GPG Key:**

   ```bash
   wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -

   ```

2. **Create a MongoDB list file:**

   ```bash
   echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -cs)/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
   ```

3. **Update the package list and install MongoDB:**

   ```bash
   sudo apt-get update
   sudo apt-get install -y mongodb-org
   ```

4. **Start MongoDB:**

   ```bash
   sudo systemctl start mongod
   ```

5. **Enable MongoDB to start on boot:**

   ```bash
   sudo systemctl enable mongod
   ```

6. **Enable MongoDB to start on boot:**

   ```bash
   sudo systemctl status mongod
   ```

7. Ensure Correct File Permissions

   ```
   sudo chown -R mongodb:mongodb /var/lib/mongodb
   sudo chown -R mongodb:mongodb /var/log/mongodb
   ```

8. Restart MongoDB

   ```
   sudo systemctl restart mongod
   ```

9. Reinstall MongoDB (If Necessary)

   ```
   sudo apt-get purge mongodb-org*
   sudo apt-get autoremove
   sudo apt-get install -y mongodb-org
   ```

## 2. Creating a MongoDB User and config mongod.conf

1. **Access the MongoDB shell:**

   ```bash
   mongosh
   ```

2. **Switch to the `admin` database:**

   ```js
   use admin
   ```

3. **Create a new user with root privileges:**

   ```js
   db.createUser({
     user: "username",
     pwd: "password",
     roles: [{ role: "root", db: "admin" }],
   });
   ```

4. **Exit the MongoDB shell:**

   ```js
   exit;
   ```

5. **Configure MongoDB to Allow Remote Connections**

   ```bash
   sudo nano /etc/mongod.conf
   ```

6. **Bind to All IP Addresses:**

   ```bash
    bindIp: 0.0.0.0
   ```

## 3. Setting Up a Replica Set

### Starting Nodes

**Primary Node**
