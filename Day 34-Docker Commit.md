# Day 34 - Docker Commit: Creating Custom Images from Containers

**Date:** 2025-09-14  
**Topic:** Save container changes as new Docker images

---

## 📖 What is `docker commit`?

The `docker commit` command creates a **new Docker image** from a container's current state. It captures all changes made to the container (files, configurations, installed packages) and saves them as a reusable image.

---

## 🎯 Basic Syntax

```bash
docker commit <container_name_or_id> <new_image_name>:<tag>
```

**Components:**
- `<container_name_or_id>` - Source container (running or stopped)
- `<new_image_name>` - Name for your new image
- `<tag>` - Version/tag (e.g., `v1.0`, `latest`, `custom`)

---

## 💻 Basic Usage

### Simple Commit Example

```bash
# Make changes in a container
docker run -it --name ubuntu_latest ubuntu:latest /bin/bash
# Inside container: install packages, modify files, etc.
apt-get update && apt-get install -y curl vim
exit

# Commit the changes to new image
docker commit ubuntu_latest myubuntu:custom

# Verify new image was created
docker images | grep myubuntu
```

### Output
```
REPOSITORY   TAG      IMAGE ID       CREATED          SIZE
myubuntu     custom   a1b2c3d4e5f6   2 seconds ago    180MB
ubuntu       latest   9876543210ab   2 weeks ago      77.8MB
```

---

## 🔧 Common Workflows

### Workflow 1: Customized Development Environment

```bash
# 1. Start base container
docker run -it --name dev_env ubuntu:latest /bin/bash

# 2. Install development tools (inside container)
apt-get update
apt-get install -y python3 python3-pip git curl vim
pip3 install requests flask
exit

# 3. Save as custom image
docker commit dev_env my_dev_env:v1.0

# 4. Use your custom image anytime
docker run -it my_dev_env:v1.0 /bin/bash
```

### Workflow 2: Web Server Configuration

```bash
# 1. Run base nginx container
docker run -it --name web_custom nginx:latest /bin/bash

# 2. Customize configuration (inside container)
echo "Custom config" > /etc/nginx/conf.d/custom.conf
echo "<h1>Custom Web Server</h1>" > /usr/share/nginx/html/index.html
exit

# 3. Commit customized server
docker commit web_custom my_nginx:configured

# 4. Run customized server
docker run -d -p 8080:80 my_nginx:configured
```

### Workflow 3: Application Snapshot

```bash
# 1. Make changes to running container
docker exec app_container bash -c "echo 'new config' > /app/config.txt"

# 2. Create snapshot with descriptive tag
docker commit app_container myapp:snapshot-$(date +%Y%m%d)

# 3. List snapshots
docker images | grep myapp
```

---

## 🎨 Advanced Options

### Adding Metadata to Commit

```bash
# Commit with author information
docker commit --author "Your Name <you@email.com>" \
  ubuntu_latest myubuntu:custom

# Commit with commit message
docker commit -m "Added curl and vim packages" \
  ubuntu_latest myubuntu:custom

# Commit with both author and message
docker commit \
  --author "DevOps Team <devops@company.com>" \
  -m "Development environment with Python 3.9" \
  dev_container mydev:python39
```

### Pausing Container During Commit

```bash
# Pause container to ensure consistent state
docker commit --pause=true ubuntu_latest myubuntu:custom

# Don't pause (faster but may be inconsistent)
docker commit --pause=false ubuntu_latest myubuntu:custom
```

### Change Image Configuration

```bash
# Commit with new CMD
docker commit --change='CMD ["python3", "app.py"]' \
  app_container myapp:v2

# Commit with new ENTRYPOINT
docker commit --change='ENTRYPOINT ["nginx", "-g", "daemon off;"]' \
  web_container myweb:v1

# Commit with environment variable
docker commit --change='ENV DEBUG=true' \
  app_container myapp:debug
```

---

## 🔍 Complete Example: Creating Custom Database Image

```bash
echo "=== Creating Custom MySQL Image ==="

# 1. Run base MySQL container
docker run -d --name mysql_custom \
  -e MYSQL_ROOT_PASSWORD=secret \
  mysql:8.0

# 2. Wait for MySQL to initialize
sleep 30

# 3. Create custom database and user
docker exec mysql_custom mysql -uroot -psecret -e "
CREATE DATABASE myapp_db;
CREATE USER 'myapp_user'@'%' IDENTIFIED BY 'myapp_pass';
GRANT ALL PRIVILEGES ON myapp_db.* TO 'myapp_user'@'%';
FLUSH PRIVILEGES;"

# 4. Commit the configured database
docker commit \
  -m "MySQL with myapp_db and user configured" \
  --author "DevOps Team" \
  mysql_custom \
  mysql_myapp:configured

# 5. Test the new image
docker stop mysql_custom && docker rm mysql_custom

docker run -d --name mysql_test \
  -p 3306:3306 \
  mysql_myapp:configured

# 6. Verify configuration persisted
docker exec mysql_test mysql -umyapp_user -pmyapp_pass -e "SHOW DATABASES;"
```

---

## ⚖️ Docker Commit vs Dockerfile

| Aspect | `docker commit` | `Dockerfile` |
|--------|----------------|--------------|
| **Creation Method** | From running container | From build instructions |
| **Best For** | Quick experiments, snapshots | Production images, repeatability |
| **Reproducibility** | Manual, hard to track | Automated, version-controlled |
| **Documentation** | Limited (commit message) | Self-documenting code |
| **Size Efficiency** | Can create larger images | Optimized with layer caching |
| **Team Collaboration** | Difficult to share process | Easy to share and review |

---

## ⚠️ Important Considerations

### When to Use `docker commit`
✅ **Good Use Cases:**
- Quick prototyping and experiments
- Creating snapshots for testing
- Capturing container state for debugging
- One-off customizations

❌ **Avoid For:**
- Production images (use Dockerfile instead)
- Team projects (hard to reproduce)
- Images that need documentation
- Complex build processes

### Best Practices

```bash
# ✅ DO: Use descriptive tags
docker commit container myimage:dev-2025-01-15

# ✅ DO: Include metadata
docker commit -m "Added SSL certificates" --author "John Doe" container myimage:v1

# ❌ DON'T: Commit without cleaning up
# First clean temporary files, then commit

# ✅ DO: Verify image before pushing
docker run -it myimage:custom /bin/bash
```

---

## 🧹 Managing Committed Images

### Viewing Image History

```bash
# See what changed in committed image
docker history myubuntu:custom

# Compare with base image
docker history ubuntu:latest
```

### Cleaning Up

```bash
# Remove old committed images
docker rmi myubuntu:old-version

# Remove unused images
docker image prune

# Remove specific image
docker rmi myubuntu:custom
```

### Sharing Your Image

```bash
# Tag for registry
docker tag myubuntu:custom username/myubuntu:custom

# Push to Docker Hub
docker push username/myubuntu:custom

# Pull on another machine
docker pull username/myubuntu:custom
```

---

## 📊 Quick Reference

```bash
# Basic commit
docker commit container_name new_image:tag

# Commit with message
docker commit -m "Description" container_name new_image:tag

# Commit with author
docker commit --author "Name" container_name new_image:tag

# Commit and pause container
docker commit --pause=true container_name new_image:tag

# Commit with config change
docker commit --change='CMD ["app"]' container_name new_image:tag

# List all images
docker images

# View image details
docker inspect new_image:tag

# Test committed image
docker run -it new_image:tag /bin/bash
```

---

## 🎯 Key Takeaways

- **`docker commit`** creates new images from container changes
- Perfect for **quick experiments** and **snapshots**
- Syntax: `docker commit <container> <image:tag>`
- Always use **descriptive tags** and **commit messages**
- For production, prefer **Dockerfiles** over commits
- Great for debugging and creating test environments

---

## 📝 Practice Exercise

Try this complete workflow:

```bash
# 1. Start container and make changes
docker run -it --name practice ubuntu:latest /bin/bash
apt-get update && apt-get install -y curl
echo "Hello from custom image" > /hello.txt
exit

# 2. Commit with metadata
docker commit \
  -m "Ubuntu with curl and custom file" \
  --author "Your Name" \
  practice \
  myubuntu:practice

# 3. Test your image
docker run -it myubuntu:practice /bin/bash
cat /hello.txt
curl --version
exit

# 4. Clean up
docker rm practice
docker rmi myubuntu:practice
```

---

*Use `docker commit` for quick iterations, but remember: Dockerfiles are the professional way to build images!* 🐳