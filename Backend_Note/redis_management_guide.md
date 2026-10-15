# Redis Server Management Guide on Pop!_OS

A comprehensive guide for managing and configuring the **Redis Server** on Pop!_OS (Ubuntu/Debian-based system).

---

## Table of Contents
1. [Checking Service Status](#1-checking-service-status)
2. [Starting, Stopping, and Restarting Redis](#2-starting-stopping-and-restarting-redis)
3. [Enabling or Disabling Auto-Start on Boot](#3-enabling-or-disabling-auto-start-on-boot)
4. [Testing Redis Connection (redis-cli)](#4-testing-redis-connection-redis-cli)
5. [Basic Redis CLI Commands](#5-basic-redis-cli-commands)
6. [Configuration & Customization](#6-configuration--customization)
7. [Security & Password Setup](#7-security--password-setup)
8. [Viewing Logs & Troubleshooting](#8-viewing-logs--troubleshooting)

---

## 1. Checking Service Status

To verify whether your Redis server is currently running:

```bash
sudo systemctl status redis-server
```

*Press `q` to exit the status view.*

---

## 2. Starting, Stopping, and Restarting Redis

### Start Redis Server
```bash
sudo systemctl start redis-server
```

### Stop Redis Server
```bash
sudo systemctl stop redis-server
```

### Restart Redis Server
```bash
sudo systemctl restart redis-server
```

---

## 3. Enabling or Disabling Auto-Start on Boot

### Enable Redis to start automatically on system boot:
```bash
sudo systemctl enable redis-server
```

### Disable Redis from starting on boot:
```bash
sudo systemctl disable redis-server
```

---

## 4. Testing Redis Connection (redis-cli)

To open the interactive Redis command-line interface:

```bash
redis-cli
```

### Quick Connectivity Test
Inside the `redis-cli`, type:

```text
PING
```

Expected response:
```text
PONG
```

To exit the CLI:
```text
exit
```

---

## 5. Basic Redis CLI Commands

Here are some essential key-value operations to run inside `redis-cli`:

### Set a Key
```text
SET mykey "Hello Pop!_OS"
```

### Get a Key Value
```text
GET mykey
```

### Delete a Key
```text
DEL mykey
```

### Check if a Key Exists
```text
EXISTS mykey
```

### Set Key with Expiration (in seconds)
```text
SETEX tempkey 60 "This expires in 60 seconds"
```

### Check Remaining TTL (Time-To-Live)
```text
TTL tempkey
```

### View All Keys (Use carefully in production)
```text
KEYS *
```

---

## 6. Configuration & Customization

The main configuration file for Redis on Pop!_OS is located at `/etc/redis/redis.conf`.

### Open Configuration File
```bash
sudo nano /etc/redis/redis.conf
```

### Allow Remote Access (Optional)
By default, Redis only listens on `127.0.0.1` (localhost). If you need remote connections:

1. Locate the line:
   ```text
   bind 127.0.0.1 ::1
   ```
2. Modify or comment it out to allow connections from external IPs (ensure firewall security first).

### Restart Redis to apply changes:
```bash
sudo systemctl restart redis-server
```

---

## 7. Security & Password Setup

### Set a Password for Redis
1. Open the configuration file:
   ```bash
   sudo nano /etc/redis/redis.conf
   ```
2. Search for the `requirepass` directive (uncomment if commented out) and set your strong password:
   ```text
   requirepass your_strong_password_here
   ```
3. Save the file (`Ctrl + O`, then `Enter`) and exit (`Ctrl + X`).
4. Restart the service:
   ```bash
   sudo systemctl restart redis-server
   ```

### Authenticate in CLI
When connecting via `redis-cli`:

```bash
redis-cli -a your_strong_password_here
```

Or authenticate inside the CLI session:
```text
AUTH your_strong_password_here
```

---

## 8. Viewing Logs & Troubleshooting

If Redis fails to start or encounters errors, check the logs using `journalctl` or inspect the Redis log file directly:

### View Real-Time Systemd Logs
```bash
sudo journalctl -u redis-server -f
```

### View Redis Log File
```bash
sudo tail -n 50 /var/log/redis/redis-server.log
```
