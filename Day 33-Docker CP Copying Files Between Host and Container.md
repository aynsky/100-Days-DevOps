# Day 33 - Docker CP: Copying Files Between Host and Container

**Date:** 2025-09-14  
**Topic:** File transfer operations with Docker containers

---

## 📖 What is `docker cp`?

The `docker cp` command copies files or directories **between your host system and running/stopped containers**. It works similar to the Linux `cp` command but across the container boundary.

---

## 🎯 Basic Syntax

```bash
# Copy FROM host TO container
docker cp <source_path> <container_name>:<destination_path>

# Copy FROM container TO host
docker cp <container_name>:<source_path> <destination_path>
```

---

## 💻 Common Use Cases

### 1. Copy File from Host to Container

```bash
# Copy a single file
docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/tmp/

# Copy with different name
docker cp /tmp/config.json ubuntu_latest:/etc/app/settings.json

# Copy entire directory
docker cp /home/user/data/ ubuntu_latest:/app/data/
```

### 2. Copy File from Container to Host

```bash
# Copy file from container
docker cp ubuntu_latest:/tmp/nautilus.txt.gpg /tmp/

# Copy container logs
docker cp ubuntu_latest:/var/log/app.log ./logs/

# Copy entire directory
docker cp ubuntu_latest:/app/output/ ./container-output/
```

### 3. Copy Between Containers (via Host)

```bash
# Extract from container1 to host
docker cp container1:/data/file.txt /tmp/file.txt

# Copy from host to container2
docker cp /tmp/file.txt container2:/data/file.txt
```

---

## 🔍 Practical Examples

### Example 1: Backup Container Data
```bash
# Backup database from container
docker cp mysql_container:/var/lib/mysql/backup.sql ./backups/

# Verify file copied
ls -lh ./backups/backup.sql
```

### Example 2: Deploy Configuration
```bash
# Copy new config to running web server
docker cp ./nginx.conf web_server:/etc/nginx/nginx.conf

# Reload configuration (if needed)
docker exec web_server nginx -s reload
```

### Example 3: Retrieve Application Logs
```bash
# Copy logs from container to analyze
docker cp app_container:/var/log/application.log ./logs/

# View the logs
tail -f ./logs/application.log
```

---

## 💡 Important Notes

### ✅ Key Features
- Works with both **running and stopped** containers
- Preserves file permissions and ownership
- Can copy entire directories recursively
- No need to mount volumes for one-time transfers

### ⚠️ Limitations
- Cannot copy from one container directly to another
- Not suitable for large-scale file synchronization
- Doesn't work with container paths that don't exist

### 🎯 Best Practices
- Use **absolute paths** to avoid confusion
- Verify container name with `docker ps`
- Check destination directory exists in container
- For frequent transfers, consider using **volumes** instead

---

## 🔧 Quick Reference

```bash
# Check container name/ID
docker ps -a

# Copy file to container
docker cp myfile.txt my_container:/path/to/destination/

# Copy file from container
docker cp my_container:/path/to/file.txt ./

# Copy directory (recursive)
docker cp ./my_folder/ my_container:/app/

# Copy with verbose output (see what's happening)
docker cp -L myfile.txt my_container:/tmp/  # Follow symlinks
```

---

## 📝 Common Scenarios

| Scenario | Command |
|----------|---------|
| Backup database | `docker cp mysql:/var/lib/mysql/dump.sql ./backup/` |
| Deploy config | `docker cp config.yml app:/etc/app/` |
| Extract logs | `docker cp app:/var/log/app.log ./logs/` |
| Copy SSL certs | `docker cp cert.pem nginx:/etc/ssl/` |
| Retrieve output | `docker cp worker:/app/output/ ./results/` |

---

## 🆚 Docker CP vs Volume Mounts

| Feature | `docker cp` | Volume Mounts |
|---------|-------------|---------------|
| **Use Case** | One-time file transfer | Persistent shared storage |
| **Performance** | Good for small files | Better for frequent access |
| **Setup** | No setup needed | Requires volume definition |
| **Flexibility** | Works anytime | Set during container creation |
| **Best For** | Ad-hoc tasks | Production deployments |

---

## 🎓 Key Takeaways

- `docker cp` is the standard tool for file transfers with containers
- Syntax mirrors Linux `cp` command with container notation
- Works bidirectionally: **host ↔ container**
- Essential for debugging, backups, and configuration updates
- For persistent data sharing, use **volume mounts** instead

---

*Master `docker cp` for quick file operations, but remember: volumes are better for persistent storage!* 🚀