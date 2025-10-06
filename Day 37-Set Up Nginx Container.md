# 🚀 Day 37 — Set Up Nginx Container

## 🎯 Task Overview
Host an application on a Nginx-based Docker container on Application Server 2, Stratos Data Center.

---

## 📋 Ticket Details:
- **Docker Image:** `nginx:stable`
- **Container Name:** `cluster`
- **Port Mapping:** Host `8084` → Container `80`
- **Server:** Application Server 2

---

## 🧩 Steps Performed:

### 1️⃣ Check Docker installation:
```bash
docker --version
sudo systemctl status docker
2️⃣ Pull nginx stable image:
bashsudo docker pull nginx:stable
3️⃣ Run container with port mapping and in detached mode:
bashsudo docker run -d \
  --name cluster \
  -p 8084:80 \
  nginx:stable
4️⃣ Verify container is running:
bashdocker ps
5️⃣ Test the nginx container:
bashcurl http://localhost:8084
You should see the default Nginx welcome page.

🏁 Final Outcome
✔️ Nginx stable image pulled successfully
✔️ Container cluster running in detached mode
✔️ Port mapping configured: 8084:80
✔️ Nginx web server accessible and responding

🧾 Notes:

The container cluster will remain running in detached mode.
Port 8084 on the host is mapped to port 80 of the container for web access.
Using nginx:stable ensures a stable version of Nginx is deployed.
To view container logs:

bashdocker logs cluster

To stop the container:

bashdocker stop cluster
