# Day 31 – Installing Docker CE and Docker Compose on App Server 1

## ❓ Task Objective
**Install docker-ce and docker compose packages on App Server 1. Initiate the docker service.**

---

## 🛠️ Installation Steps

### 1. Remove Old Docker Versions (if any)

```bash
sudo yum remove -y docker docker-client docker-client-latest docker-common docker-latest \
    docker-latest-logrotate docker-logrotate docker-engine
```

**Purpose:** Clean up any existing Docker installations to prevent conflicts

### 2. Add the Official Docker CE Repository

```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

**What this does:**
- Installs `yum-utils` for repository management
- Adds Docker's official CentOS repository

### 3. Install Docker CE, CLI, and containerd

```bash
sudo yum install -y docker-ce docker-ce-cli containerd.io
```

**Components installed:**
- `docker-ce`: Docker Community Edition engine
- `docker-ce-cli`: Command line interface
- `containerd.io`: Container runtime

### 4. Install Docker Compose Plugin

```bash
sudo yum install -y docker-compose-plugin
```

**Verification:**
Check versions:
```bash
docker --version
docker compose version
```

### 5. Enable and Start Docker Service

```bash
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker
```

**Service management:**
- `enable`: Start Docker automatically on boot
- `start`: Start Docker service immediately
- `status`: Verify service is running

### 6. Verify Installation

```bash
docker run hello-world
```

This should confirm that Docker is installed and running correctly.

---

## 🔍 Verification Checklist

| Component | Verification Command | Expected Result |
|-----------|---------------------|----------------|
| **Docker Engine** | `docker --version` | Docker version XX.X.X |
| **Docker Compose** | `docker compose version` | Docker Compose version vX.X.X |
| **Service Status** | `sudo systemctl status docker` | `active (running)` |
| **Runtime Test** | `docker run hello-world` | Success message from hello-world container |

---

## ❓ FAQ: Package Manager Differences

### Why can't we just use `dnf` instead of `yum`?

**Answer:**
- On **RHEL/CentOS 7**, `yum` is the real package manager
- On **RHEL/CentOS 8/9**, `yum` is just a **symlink to** `dnf` — so whether you type `yum` or `dnf`, both run the same backend
- On **Fedora**, only `dnf` exists

**Conclusion:** In modern RHEL/CentOS, you can safely use either.

---

## 📋 System Requirements

### Supported Operating Systems
- CentOS 7, 8, 9
- RHEL 7, 8, 9
- Rocky Linux 8, 9
- AlmaLinux 8, 9

### Minimum Requirements
- **Kernel:** 3.10+ (for CentOS 7) or 4.18+ (for CentOS 8+)
- **Architecture:** x86_64/amd64
- **Storage:** At least 10GB free disk space
- **RAM:** Minimum 2GB recommended

---

## 🔧 Troubleshooting

### Common Issues & Solutions

| Issue | Symptoms | Solution |
|-------|----------|----------|
| **Repository not found** | `No package docker-ce available` | Verify repository was added correctly |
| **Permission denied** | `permission denied while trying to connect` | Add user to docker group: `sudo usermod -aG docker $USER` |
| **Service won't start** | `Failed to start docker.service` | Check logs: `sudo journalctl -u docker.service` |
| **Old Docker conflicts** | Package conflicts during installation | Ensure step 1 (removal) was completed |

### Debug Commands
```bash
# Check repository configuration
sudo yum repolist | grep docker

# View detailed service status
sudo systemctl status docker -l

# Check Docker daemon logs
sudo journalctl -u docker.service --no-pager

# Verify Docker socket
sudo ls -la /var/run/docker.sock
```

---

## 🚀 Post-Installation Configuration

### Add User to Docker Group (Optional)
```bash
# Add current user to docker group
sudo usermod -aG docker $USER

# Apply group changes (logout/login required)
newgrp docker

# Test without sudo
docker run hello-world
```

### Configure Docker Daemon (Optional)
Create `/etc/docker/daemon.json` for custom configuration:
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  },
  "storage-driver": "overlay2"
}
```

### Start Docker on Boot
```bash
# Verify Docker is enabled for autostart
sudo systemctl is-enabled docker

# If not enabled, enable it
sudo systemctl enable docker
```

---

## 📊 Installation Summary

### What Was Installed
| Package | Purpose | Location |
|---------|---------|----------|
| `docker-ce` | Docker engine | `/usr/bin/dockerd` |
| `docker-ce-cli` | Command line tools | `/usr/bin/docker` |
| `containerd.io` | Container runtime | `/usr/bin/containerd` |
| `docker-compose-plugin` | Compose functionality | `/usr/libexec/docker/cli-plugins/docker-compose` |

### Services Started
- **docker.service**: Main Docker daemon
- **containerd.service**: Container runtime service

---

## ✅ Final Outcome

- ✅ **Docker CE installed** on App Server 1
- ✅ **Docker Compose plugin** installed and functional
- ✅ **Docker service** enabled and started
- ✅ **Installation verified** with hello-world test image
- ✅ **System ready** for containerized applications

---

## 🔗 Useful Docker Commands

### Basic Docker Operations
```bash
# List running containers
docker ps

# List all containers
docker ps -a

# List images
docker images

# Pull an image
docker pull nginx

# Run a container
docker run -d nginx

# Stop a container
docker stop <container-id>

# Remove a container
docker rm <container-id>
```

### Docker Compose Operations
```bash
# Start services defined in docker-compose.yml
docker compose up -d

# Stop services
docker compose down

# View logs
docker compose logs

# List services
docker compose ps
```

---

*Docker installation completed successfully. The system is now ready for containerized application deployment and management.*