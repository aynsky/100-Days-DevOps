# 🚀 Day 35 — Custom Apache Docker Image (Ubuntu 24.04)

## 🎯 Task Overview
As per the recent Nautilus application requirements, we needed to create a **custom Docker image** for testing. The DevOps team was asked to configure a Dockerfile on *App Server 2* with the following specs:
* Base image: **ubuntu:24.04**
* Install and configure **Apache2**
* Change Apache port to **6100**
* Keep all other Apache configurations default
* Build and run the container successfully

## 🧩 Steps Performed

### 1️⃣ Create directory and Dockerfile
```bash
sudo mkdir -p /opt/docker
sudo vi /opt/docker/Dockerfile
2️⃣ Dockerfile content
dockerfileFROM ubuntu:24.04
RUN apt-get update && apt-get install -y apache2

# Configure Apache to listen on port 6100
RUN sed -i 's/80/6100/' /etc/apache2/ports.conf \
    && sed -i 's/:80/:6100/' /etc/apache2/sites-available/000-default.conf

# Prevent "Could not reliably determine FQDN" warning
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

EXPOSE 6100

# Run Apache in foreground (important for Docker)
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
⚙️ 3️⃣ Build Docker Image
bashsudo docker build -t custom-apache /opt/docker
Verify:
bashsudo docker images
Output:
REPOSITORY      TAG       IMAGE ID       CREATED              SIZE
custom-apache   latest    fa2eacda0922   1 minute ago         254MB
ubuntu          24.04     602eb6fb314b   6 months ago         78.1MB
🚀 4️⃣ Run Container
bashsudo docker run -d --name custom_apache -p 6100:6100 custom-apache
Check if it's running:
bashsudo docker ps
🔍 5️⃣ Verify Apache Process
bashsudo docker exec -it custom_apache ps aux | grep apache2
Expected output:
root         1  0.0  0.1  71964  4768 ?        Ss   10:02   0:00 /usr/sbin/apache2 -D FOREGROUND
www-data     7  0.0  0.1  71964  4768 ?        S    10:02   0:00 /usr/sbin/apache2 -D FOREGROUND
🧠 6️⃣ Common Issue Encountered
When checking container logs:
AH00558: apache2: Could not reliably determine the server's fully qualified domain name
✅ Cause: Apache couldn't find a hostname.
✅ Fix: Added RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf to Dockerfile.
Rebuilt the image → issue resolved.
🧪 7️⃣ Test the Web Server
bashcurl http://localhost:6100
Output:
It works!
🏁 Final Outcome
✔️ Custom Docker image successfully built using Ubuntu 24.04
✔️ Apache configured on port 6100
✔️ Warning resolved
✔️ Container verified running and serving web requests
🧾 Notes

systemctl does not work inside Docker containers — use foreground commands like:

bashapache2ctl -D FOREGROUND

Always ensure the main service runs in the foreground; otherwise, the container will exit immediately.