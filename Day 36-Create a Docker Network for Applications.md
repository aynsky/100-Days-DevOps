# 🚀 Day 36 — Create a Docker Network for Applications

## 🎯 Task Overview
The Nautilus DevOps team needs to set up several Docker environments. For this task, we need to create a Docker network that can be used by future containers.

---

## 📋 Ticket Details:
- **Network Name:** `blog`
- **Driver:** `bridge`
- **Subnet:** `10.10.1.0/24`
- **IP Range:** `10.10.1.0/24`
- **Server:** App Server 1, Stratos Data Center

---

## 🧩 Steps Performed:

### 1️⃣ Verify Docker installation and service status:
```bash
docker --version
sudo systemctl status docker
2️⃣ Create the Docker network with specified configurations:
bashsudo docker network create \
  --driver bridge \
  --subnet 10.10.1.0/24 \
  --ip-range 10.10.1.0/24 \
  blog
3️⃣ Verify the network creation:
List all Docker networks:
bashdocker network ls
Inspect the specific network details:
bashdocker network inspect blog
📤 Expected Output:
json[
    {
        "Name": "blog",
        "Id": "abcdef123456...",
        "Driver": "bridge",
        "IPAM": {
            "Driver": "default",
            "Config": [
                {
                    "Subnet": "10.10.1.0/24",
                    "IPRange": "10.10.1.0/24"
                }
            ]
        }
    }
]

🏁 Final Outcome
✔️ Docker network blog created successfully
✔️ Bridge driver configured
✔️ Custom subnet and IP range assigned
✔️ Network ready for container deployment

🧾 Notes:

The bridge driver allows containers to communicate within the network while remaining isolated from external networks.
The subnet and IP range ensure proper IP allocation for containers attached to this network.
Future containers can connect to this network using:

bashdocker run --network blog <image_name>