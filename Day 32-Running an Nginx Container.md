# Day 32 - Running an Nginx Container (Alpine)

## ✅ Task Objective
On **Application Server 1**, create a Docker container named `nginx_1` using the `nginx` image with the **alpine** tag. Ensure that the container is in a running state.

---

## 🛠️ Implementation Steps

### 1. Start Docker Service
```bash
sudo systemctl start docker
sudo systemctl enable docker
```

**Purpose:**
- Starts Docker daemon immediately
- Enables Docker to start automatically on system boot

### 2. Pull the Nginx Alpine Image
```bash
sudo docker pull nginx:alpine
```

**What this does:**
- Downloads the nginx image with alpine tag from Docker Hub
- Alpine Linux provides a minimal base image (~5MB vs standard ~133MB)

### 3. Run the Container
```bash
sudo docker run -d --name nginx_1 nginx:alpine
```

**Command breakdown:**
- `-d` → Run in detached mode (background)
- `--name nginx_1` → Name the container `nginx_1`
- `nginx:alpine` → Use the nginx image with alpine tag

### 4. Verify the Container Status
```bash
sudo docker ps
```

**Expected output:**
```
CONTAINER ID   IMAGE          COMMAND                  STATUS         PORTS   NAMES
abcd1234efgh   nginx:alpine   "/docker-entrypoint.…"   Up 10 seconds          nginx_1
```

---

## 🔍 Verification & Testing

### Check Container Status
```bash
# List running containers
sudo docker ps

# View all containers (including stopped)
sudo docker ps -a

# Inspect container details
sudo docker inspect nginx_1

# Check container logs
sudo docker logs nginx_1
```

### Test Nginx Inside Container
```bash
# Execute command inside container
sudo docker exec nginx_1 nginx -v

# Access container shell
sudo docker exec -it nginx_1 /bin/sh

# Test Nginx configuration
sudo docker exec nginx_1 nginx -t
```

---

## 🌐 Port Mapping (Optional Enhancement)

### Basic Port Exposure
By default, Nginx listens on **port 80** inside the container but is not accessible from the host.

To expose it externally:
```bash
sudo docker run -d --name nginx_1 -p 8080:80 nginx:alpine
```

**Port mapping explained:**
- `-p 8080:80` → Maps host port 8080 to container port 80
- Access via: `http://localhost:8080`

### Common Port Mapping Scenarios
```bash
# Standard HTTP port
sudo docker run -d --name nginx_1 -p 80:80 nginx:alpine

# Custom host port
sudo docker run -d --name nginx_1 -p 8080:80 nginx:alpine

# HTTPS with port mapping
sudo docker run -d --name nginx_1 -p 80:80 -p 443:443 nginx:alpine

# Bind to specific IP
sudo docker run -d --name nginx_1 -p 127.0.0.1:8080:80 nginx:alpine
```

---

## 📊 Container Management Commands

### Lifecycle Operations
```bash
# Stop the container
sudo docker stop nginx_1

# Start the container
sudo docker start nginx_1

# Restart the container
sudo docker restart nginx_1

# Pause the container
sudo docker pause nginx_1

# Unpause the container
sudo docker unpause nginx_1

# Remove the container (must be stopped first)
sudo docker rm nginx_1

# Force remove running container
sudo docker rm -f nginx_1
```

### Monitoring & Logs
```bash
# View real-time logs
sudo docker logs -f nginx_1

# View last 50 lines of logs
sudo docker logs --tail 50 nginx_1

# View logs with timestamps
sudo docker logs -t nginx_1

# Check container resource usage
sudo docker stats nginx_1

# View container processes
sudo docker top nginx_1
```

---

## 📌 Technical Notes

### Why Alpine?
| Aspect | Alpine | Standard Debian |
|--------|--------|-----------------|
| **Image Size** | ~5-7 MB | ~130-140 MB |
| **Base OS** | Alpine Linux | Debian/Ubuntu |
| **Package Manager** | apk | apt |
| **Startup Time** | Faster | Slower |
| **Use Case** | Production, microservices | Development, compatibility |

### Container Configuration
- **Default Port:** 80 (HTTP)
- **Default Config:** `/etc/nginx/nginx.conf`
- **Document Root:** `/usr/share/nginx/html`
- **Log Location:** `/var/log/nginx/`

### Nginx Inside Container
```bash
# View Nginx version
sudo docker exec nginx_1 nginx -v

# Test configuration
sudo docker exec nginx_1 nginx -t

# Reload configuration
sudo docker exec nginx_1 nginx -s reload

# Stop Nginx gracefully
sudo docker exec nginx_1 nginx -s quit
```

---

## 🚀 Advanced Configurations

### Mount Custom HTML Content
```bash
sudo docker run -d --name nginx_1 \
  -p 8080:80 \
  -v /path/to/html:/usr/share/nginx/html:ro \
  nginx:alpine
```

### Mount Custom Nginx Configuration
```bash
sudo docker run -d --name nginx_1 \
  -p 8080:80 \
  -v /path/to/nginx.conf:/etc/nginx/nginx.conf:ro \
  nginx:alpine
```

### Environment Variables
```bash
sudo docker run -d --name nginx_1 \
  -p 8080:80 \
  -e NGINX_HOST=example.com \
  -e NGINX_PORT=80 \
  nginx:alpine
```

### With Resource Limits
```bash
sudo docker run -d --name nginx_1 \
  -p 8080:80 \
  --memory="256m" \
  --cpus="0.5" \
  nginx:alpine
```

---

## 🔧 Troubleshooting

### Common Issues & Solutions

| Issue | Symptoms | Solution |
|-------|----------|----------|
| **Port already in use** | `bind: address already in use` | Use different host port or stop conflicting service |
| **Container exits immediately** | Container not in `docker ps` | Check logs: `sudo docker logs nginx_1` |
| **Permission denied** | Cannot start Docker | Run with `sudo` or add user to docker group |
| **Image not found** | `Unable to find image` | Run `docker pull nginx:alpine` first |

### Debug Commands
```bash
# Check if port is already in use
sudo netstat -tlnp | grep :80

# View Docker daemon logs
sudo journalctl -u docker -f

# Inspect container details
sudo docker inspect nginx_1

# Check container exit code
sudo docker ps -a --filter name=nginx_1 --format "{{.Status}}"

# View container resource usage
sudo docker stats nginx_1 --no-stream
```

---

## 📚 Learning Outcomes

### Key Concepts Mastered
✅ **Image Tags** - Learned how to use specific image tags (`nginx:alpine`)  
✅ **Container Naming** - Understanding the importance of meaningful container names  
✅ **Detached Mode** - Running containers in background vs foreground  
✅ **Port Mapping** - Difference between internal and external port exposure  
✅ **Container Lifecycle** - Starting, stopping, and managing container states  
✅ **Alpine Linux** - Benefits of minimal base images for production

### Practical Skills Gained
- Using Docker Hub to pull specific image versions
- Running containers with custom configurations
- Verifying container status and health
- Understanding container networking basics
- Troubleshooting common container issues

---

## 🎯 Quick Reference

### Essential Commands
```bash
# Create and run container
sudo docker run -d --name nginx_1 nginx:alpine

# Check status
sudo docker ps

# View logs
sudo docker logs nginx_1

# Access shell
sudo docker exec -it nginx_1 /bin/sh

# Stop container
sudo docker stop nginx_1

# Remove container
sudo docker rm nginx_1
```

### With Port Mapping
```bash
# Run with external access
sudo docker run -d --name nginx_1 -p 8080:80 nginx:alpine

# Test from host
curl http://localhost:8080
```

---

## ✅ Task Completion Checklist

- [x] Docker service started and enabled
- [x] Nginx alpine image pulled successfully
- [x] Container `nginx_1` created with correct name
- [x] Container running in detached mode
- [x] Container status verified with `docker ps`
- [x] Nginx responding correctly (if port mapped)

---

*Successfully deployed a lightweight Nginx web server using Alpine Linux base image. Container is running, manageable, and ready for production workloads.*