# MongoDB Installation and Replica Set Setup

## Introduction

This guide provides step-by-step instructions for installing MongoDB, creating a user, and configuring a replica set on Ubuntu Server. It covers both standalone and configuration file-based deployments.

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

6. **Check the status of MongoDB:**

   ```bash
   sudo systemctl status mongod
   ```

7. **Ensure Correct File Permissions:**

   ```bash
   sudo chown -R mongodb:mongodb /var/lib/mongodb
   sudo chown -R mongodb:mongodb /var/log/mongodb
   ```

8. **Restart MongoDB:**

   ```bash
   sudo systemctl restart mongod
   ```

9. **Reinstall MongoDB (If Necessary):**

   ```bash
   sudo apt-get purge mongodb-org*
   sudo apt-get autoremove
   sudo apt-get install -y mongodb-org
   ```

10. **MongoDB.service failed with result exit-code**

```bash
sudo rm -rf /tmp/mongodb-27017.sock
sudo systemctl start mongod
sudo systemctl status mongod
```

## Step 2: Creating a MongoDB User and Configuring `mongod.conf`

1. **Access the MongoDB shell:**

   ```bash
   mongosh
   ```

2. **Switch to the `admin` database:**

   ```javascript
   use admin
   ```

3. **Create a new user with root privileges:**

   ```javascript
   db.createUser({
     user: "username",
     pwd: "password",
     roles: [{ role: "root", db: "admin" }],
   });
   ```

4. **Exit the MongoDB shell:**

   ```javascript
   exit;
   ```

5. **Configure MongoDB to Allow Remote Connections:**

   ```bash
   sudo nano /etc/mongod.conf
   ```

6. **Bind to All IP Addresses and Change the MongoDB Default Port:**

   In the `net` section, set `bindIp` to `127.0.0.1`:

   ```yaml
   net:
     port: 27017
     bindIp: 127.0.0.1
   ```

7. **Check MongoDB Logs:**

   ```bash
   sudo cat /var/log/mongodb/mongod.log
   ```

## Step 3: MongoDB Connection Details

To connect to your MongoDB instance using MongoDB Compass, follow these instructions:

### Connection String

Use the following connection string in MongoDB Compass, replacing `<username>`, `<password>`, and `<host>:<port>` with your actual credentials and server details:

```plaintext
mongodb://<username>:<password>@<host>:<port>/?authSource=admin
```

### MongoDB Connection Error Troubleshooting

If you encounter a connection error due to MongoDB running on a non-default port, follow these steps:

1. **Update MongoDB Connection String:**

   If you changed the port to `25017`, update your connection string accordingly:

   ```bash
   mongodb://127.0.0.1:25017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.3.0
   ```

2. **Connect to MongoDB with an Admin User:**

   ```bash
   mongosh "mongodb://<username>:<password>@<hostname>:<port>/?authSource=admin"
   ```

## Step 4: Changing and Deleting the MongoDB Admin Password

### 1. Authenticate as an Admin

If you didn't authenticate directly in the connection string, authenticate after connecting:

```javascript
use admin
db.auth("admin", "<current_password>")
```

### 2. Change the Password

Change the password for any user, including the `admin` user:

```javascript
db.updateUser("admin", { pwd: "<new_password>" });
```

### 3. Verify the Password Change

Disconnect and reconnect using the new password to verify the change:

```bash
mongosh "mongodb://admin:NewSecurePassword123@40.90.161.121:28017/?authSource=admin"
```

### 4. Drop a MongoDB User

To delete a user:

```javascript
db.dropUser("USER");
```

### 5. Close the Connection

After confirming the password change, safely close the connection:

```bash
exit
```

# 6 MongoDB Replica Set Local Ubuntu Machine Setup Guide

## Introduction

This guide provides step-by-step instructions to set up a MongoDB replica set on a local Ubuntu server. A replica set is a group of MongoDB instances that maintain the same data set, providing redundancy and high availability.

## Prerequisites

- Ubuntu server with MongoDB installed.
- Basic knowledge of MongoDB and Ubuntu command-line operations.

## Step 1: Create Directories for MongoDB Instances

Create separate directories for each MongoDB instance’s data and log files:

```bash
sudo mkdir -p /data/rs1 /data/rs2 /data/rs3
sudo mkdir -p /var/log/mongodb
```

## Step 2: Configure MongoDB Instances

Create configuration files for each MongoDB instance.

### \`mongod1.conf\`

```yaml
storage:
  dbPath: /data/rs1
systemLog:
  destination: file
  path: /var/log/mongodb/mongod1.log
net:
  port: 27001
  bindIp: localhost
replication:
  replSetName: "rs0"
```

### \`mongod2.conf\`

```yaml
storage:
  dbPath: /data/rs2
systemLog:
  destination: file
  path: /var/log/mongodb/mongod2.log
net:
  port: 27002
  bindIp: localhost
replication:
  replSetName: "rs0"
```

### \`mongod3.conf\`

```yaml
storage:
  dbPath: /data/rs3
systemLog:
  destination: file
  path: /var/log/mongodb/mongod3.log
net:
  port: 27003
  bindIp: localhost
replication:
  replSetName: "rs0"
```

## Step 3: Start the MongoDB Instances

Start each MongoDB instance using the configuration files:

```bash
sudo mongod --config /etc/mongod1.conf --fork
sudo mongod --config /etc/mongod2.conf --fork
sudo mongod --config /etc/mongod3.conf --fork
```

## Step 4: Initiate the Replica Set

1. **Connect to the first MongoDB instance:**

   ```bash
   mongosh --port 27001
   ```

2. **Initiate the replica set:**

   ```javascript
   rs.initiate({
     _id: "rs0",
     members: [
       { _id: 0, host: "localhost:27001" },
       { _id: 1, host: "localhost:27002" },
       { _id: 2, host: "localhost:27003" },
     ],
   });
   ```

3. **Check the status of the replica set:**

   ```javascript
   rs.status();
   ```

## Step 5: Verify the Replica Set

To ensure that everything is set up correctly, you can connect to any of the MongoDB instances and check the replica set status.

```bash
mongo --port 27001
```

Inside the MongoDB shell, run:

```javascript
rs.status();
```

## Step 6: Access the Replica Set

To connect to the replica set from your application or another MongoDB shell, use the following connection string:

```bash
mongo --host "localhost:27001,localhost:27002,localhost:27003" --replSet rs0
```

## Important Notes

- **Backup:** Always back up your MongoDB database before making changes to critical user accounts.
- **Special Characters:** URL-encode special characters in passwords when using them in connection strings.
- **Security:** Use strong passwords to secure your MongoDB instance.
