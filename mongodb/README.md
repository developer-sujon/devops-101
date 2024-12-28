# MongoDB Installation Guide

## Introduction

This guide provides step-by-step instructions for installing MongoDB on an Ubuntu Server. It is designed to help you set up MongoDB quickly and ensure it runs correctly.

## Prerequisites

- **Ubuntu Server** (20.04 or later recommended)
- **SSH Access** to the server
- Basic knowledge of Linux command-line operations

## Step 1: Install MongoDB

### 1. Import the MongoDB GPG Key

```bash
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
```

### 2. Create a MongoDB List File

```bash
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -cs)/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```

### 3. Update the Package List and Install MongoDB

```bash
sudo apt-get update
sudo apt-get install -y mongodb-org
```

### 4. Start MongoDB

```bash
sudo systemctl start mongod
```

### 5. Enable MongoDB to Start on Boot

```bash
sudo systemctl enable mongod
```

### 6. Check the Status of MongoDB

```bash
sudo systemctl status mongod
```

### 7. Ensure Correct File Permissions (Optional)

```bash
sudo chown -R mongodb:mongodb /var/lib/mongodb
sudo chown -R mongodb:mongodb /var/log/mongodb
```

### 8. Restart MongoDB (If Necessary)

```bash
sudo systemctl restart mongod
```

### 9. Reinstall MongoDB (If Needed)

If you encounter installation issues, you can remove and reinstall MongoDB:

```bash
sudo apt-get purge mongodb-org*
sudo apt-get autoremove
sudo apt-get install -y mongodb-org
```

### 10. Resolve Common Errors

If MongoDB fails to start with a socket-related error:

```bash
sudo rm -rf /tmp/mongodb-27017.sock
sudo systemctl start mongod
sudo systemctl status mongod
```

## Step 2: Creating a MongoDB User and Configuring `mongod.conf`

### 1. Access the MongoDB Shell

```bash
mongosh
```

### 2. Switch to the `admin` Database

```javascript
use admin
```

### 3. Create a New User with Root Privileges

```javascript
db.createUser({
  user: "username",
  pwd: "password",
  roles: [{ role: "root", db: "admin" }],
});
```

### 4. Exit the MongoDB Shell

```javascript
exit;
```

### 5. Configure MongoDB to Allow Remote Connections

Edit the `mongod.conf` file:

```bash
sudo nano /etc/mongod.conf
```

Update the `net` section to bind to all IP addresses:

```yaml
net:
  port: 27017
  bindIp: 0.0.0.0
```

### 6. Restart MongoDB to Apply Changes

```bash
sudo systemctl restart mongod
```

## Step 3: MongoDB Connection Details

### 1. Connection String for MongoDB Compass

Use the following connection string, replacing `<username>`, `<password>`, and `<host>` with your credentials and server details:

```plaintext
mongodb://<username>:<password>@<host>:27017/?authSource=admin
```

### 2. Connecting via Command Line

If using a custom port (e.g., `25017`):

```bash
mongosh "mongodb://<username>:<password>@<host>:25017/?authSource=admin"
```

## Step 4: Changing and Deleting the MongoDB Admin Password

### 1. Authenticate as an Admin

```javascript
use admin
db.auth("admin", "<current_password>");
```

### 2. Change the Admin Password

```javascript
db.updateUser("admin", { pwd: "<new_password>" });
```

### 3. Verify the Password Change

Disconnect and reconnect with the new password:

```bash
mongosh "mongodb://admin:<new_password>@<host>:27017/?authSource=admin"
```

### 4. Delete a MongoDB User

To drop a user:

```javascript
db.dropUser("username");
```

### 5. Safely Close the Connection

```bash
exit
```

## Notes

- **Security:** Ensure you configure strong access controls if exposing MongoDB to the internet.
- **Logs:** Check MongoDB logs for errors:

```bash
sudo cat /var/log/mongodb/mongod.log
```

This completes the MongoDB installation process. For additional configurations, refer to the MongoDB documentation.

