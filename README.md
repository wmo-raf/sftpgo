# SFTPGo Installation Guide

SFTPGo is a lightweight, production-ready SFTP/FTP server that makes secure file sharing effortless.

More details [https://sftpgo.com](https://sftpgo.com)

## Step-by-Step Setup Instructions

---

## Prerequisites

Before you begin, ensure you have the following installed on your system:

1. **Docker** (version 20.10+)
2. **Docker Compose** (version 1.29+)
3. **Git** (to clone repositories or download files)
4. **A Linux server** (Ubuntu 20.04+ or CentOS 7+ recommended)
5. **Internet connection** to download Docker images

### Check if you have Docker and Docker Compose installed:

```bash
docker --version
docker-compose --version
```

If not installed, follow the [official Docker installation guide](https://docs.docker.com/get-docker/).

---

## Step 1: Create Project Directory Structure

Create a folder where all SFTPGo files will be stored:

```bash
mkdir -p sftpgo
cd sftpgo
```

Create subdirectories for data persistence:

```bash
mkdir -p data/sftpgodata
mkdir -p data/sftpgohome
mkdir -p pg_data
```

Your directory structure should look like this:

```
sftpgo/
├── data/
│   ├── sftpgodata/
│   └── sftpgohome/
├── pg_data/
├── docker-compose.yml
└── .env
```

---

## Step 2: Create the .env Configuration File

Create a file named `.env` in your `sftpgo` directory:

You can copy from the sample file

```bash
cp .env.sample .env
```

Update the following variables:

```
DB_USER=sftpgo
DB_NAME=sftpgo
DB_PASSWORD=your_secure_password_here
NETWORK_NAME=sftpgo_network
```

**Important:** Replace `your_secure_password_here` with a strong password. Use a combination of:
- Uppercase letters (A-Z)
- Lowercase letters (a-z)
- Numbers (0-9)
- Special characters (!@#$%^&*)

Save the file (in nano: press `Ctrl+O`, then `Enter`, then `Ctrl+X`).

---

## Step 4: Create the Docker Network (First Time Only)

The configuration uses an external network. Create it with this command (use the NETWORK_NAME from your .env file):

```bash
docker network create sftpgo_network
```

Or if you changed `NETWORK_NAME` in your .env file, replace it accordingly:

```bash
docker network create your_custom_network_name
```

To verify the network was created:

```bash
docker network ls
```

---

## Step 5: Set Directory Permissions

Set proper permissions for the data directories:

```bash
chmod 755 data/sftpgodata
chmod 755 data/sftpgohome
chmod 755 pg_data
```

---

## Step 6: Start the Services

Navigate to your project directory and start all services:

```bash
cd sftpgo
docker-compose up -d
```

The `-d` flag runs containers in detached mode (background).

Monitor the startup process:

```bash
docker-compose logs -f
```

Wait until you see messages indicating both services are running. Press `Ctrl+C` to exit the logs.

---

## Step 7: Verify Installation

Check if both containers are running:

```bash
docker-compose ps
```

Expected output:
```
CONTAINER ID   IMAGE                    STATUS
xxxxx          drakkan/sftpgo   Up 2 minutes
xxxxx          postgres:15              Up 2 minutes
```

Check the logs for any errors:

```bash
docker-compose logs sftpgo
docker-compose logs sftpgo-db
```

---

## Step 8: Access SFTPGo Web Interface

Open your web browser and go to:

```
http://your-server-ip:8080
```

Replace `your-server-ip` with your server's actual IP address.

**Default Admin Credentials:**
- Username: `admin`
- Password: `password`

**⚠️ IMPORTANT: Change default credentials immediately after first login!**

---

## Step 9: Create Administrator User

1. Log in with default credentials (admin/password)
2. Go to "Admins" section
3. Create a new admin account with a strong password
4. Delete the default admin account

---

## Port Configuration Reference

Your SFTPGo setup uses these ports:

| Port Range | Service | Purpose |
|-----------|---------|---------|
| 8080 | HTTP | Web administration interface |
| 2022 | SSH/SFTP | Secure file transfer (primary) |
| 2121 | FTP | File transfer protocol |
| 50000-50100 | FTP Passive | FTP passive mode data connections |

Ensure these ports are:
1. Not blocked by firewall
2. Open in your hosting provider's security groups
3. Accessible from your network

---

## Stopping and Starting Services

### Stop all services:
```bash
docker-compose down
```

### Start services again:
```bash
docker-compose up -d
```

### Restart services:
```bash
docker-compose restart
```

### View logs:
```bash
docker-compose logs -f sftpgo
docker-compose logs -f sftpgo-db
```

---

## Troubleshooting

### Issue: Cannot access web interface (http://ip:8080)

**Solution:**
1. Check if containers are running: `docker-compose ps`
2. Check firewall settings: `sudo ufw status`
3. Allow port 8080: `sudo ufw allow 8080`
4. Check service logs: `docker-compose logs sftpgo`

### Issue: Database connection error

**Solution:**
1. Verify .env file has correct values: `cat .env`
2. Check database logs: `docker-compose logs sftpgo-db`
3. Restart database: `docker-compose restart sftpgo-db`

### Issue: Permission denied errors

**Solution:**
```bash
sudo chown -R 1002:1002 data/sftpgodata
sudo chown -R 1002:1002 data/sftpgohome
```

### Issue: Docker network not found

**Solution:**
Recreate the network using the name from your .env file (NETWORK_NAME):
```bash
docker network create sftpgo_network
docker-compose down
docker-compose up -d
```

---

## Security Best Practices

1. **Change default credentials immediately**
2. **Use strong passwords** (minimum 16 characters)
3. **Configure firewall** to restrict access to necessary ports only
4. **Use SSH keys** instead of passwords for SFTP access
5. **Enable defender** (already enabled in this config)
6. **Keep Docker images updated**:
   ```bash
   docker-compose pull
   docker-compose up -d
   ```
7. **Monitor logs regularly** for suspicious activity
8. **Use HTTPS** with proper SSL certificates for web interface

---

## Regular Maintenance

### Update containers weekly:

```bash
docker-compose pull
docker-compose up -d
```

### Clean up unused Docker resources:

```bash
docker system prune -a
```

### Monitor disk space:

```bash
df -h
du -sh *
```

---

## Support and Documentation

- **SFTPGo Official Documentation:** https://sftpgo.github.io/
- **Docker Documentation:** https://docs.docker.com/
- **PostgreSQL Documentation:** https://www.postgresql.org/docs/

---

## Next Steps

1. Create SFTP user accounts in the web interface
2. Set directory quotas and permissions for users
3. Configure backup strategy
4. Set up monitoring and alerting
5. Document all usernames and access credentials in a secure location

---