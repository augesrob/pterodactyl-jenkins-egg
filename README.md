# 🧩 Jenkins Pterodactyl Egg

This repository provides a ready-to-use **Pterodactyl Egg** for running **Jenkins LTS** (latest stable) inside an isolated Docker environment using the official `eclipse-temurin:17-jdk` image.

---

## 📦 Overview

Jenkins is a self-hosted automation server used for building, testing, and deploying code.  
This egg installs Jenkins LTS automatically and configures it to run on your specified Pterodactyl port.

The installer downloads the stable `jenkins.war`, installs **OpenJDK 17**, and configures it for 0.0.0.0 access to make it reachable from the panel.

---

## ⚙️ Server Specifications

| Setting | Recommended Value |
|----------|------------------|
| **Base Docker Image** | `eclipse-temurin:17-jdk` |
| **Startup Command** | `java -Djava.util.prefs.userRoot=/tmp -jar jenkins.war --httpPort={{SERVER_PORT}} --httpListenAddress=0.0.0.0` |
| **Installer Script Base Image** | `ghcr.io/pterodactyl/installers:debian` |
| **Architecture** | `amd64` (x86_64) |
| **Default Port** | `8123` |
| **Environment Variable (SERVER_PORT)** | required, default = `8123` |
| **Minimum Memory (RAM)** | 2 GB (recommended 4 GB for medium CI loads) |
| **Storage Requirement** | ~2 GB (base install) + 10 GB for build workspace |
| **CPU Cores** | 2+ |
| **Java Runtime** | OpenJDK 17 (Temurin JDK 17) |
| **Operating System** | Debian 11 (Bullseye) or Debian 12 (Bookworm) |

---

## 📂 File Structure

When installed, files are placed under:
/mnt/server/
├── jenkins.war
├── logs/
├── plugins/
└── jobs/


Persistent data is stored automatically inside the container’s `/var/jenkins_home` (mapped to Pterodactyl’s `/mnt/server/`).

---

## 🛠️ Installation Script Summary

The installation script:

1. Updates apt repositories.
2. Installs `curl`, `ca-certificates`, and `openjdk-17-jdk`.
3. Downloads Jenkins LTS WAR from [`https://get.jenkins.io/war-stable/latest/jenkins.war`](https://get.jenkins.io/war-stable/latest/jenkins.war).
4. Verifies checksum (optional step).
5. Saves it to `/mnt/server/jenkins.war`.

Fallback logic is included if the primary download fails (uses version 2.479.2 as backup).

---

## 🧠 Startup Details

The startup command ensures:
- Jenkins listens on **all interfaces (`0.0.0.0`)**.
- Preferences directory errors are avoided by redirecting prefs to `/tmp`:
  ```bash
  -Djava.util.prefs.userRoot=/tmp

🔒 Ports
Port	Protocol	Purpose
8123	TCP	Web dashboard
50000	TCP	(Optional) Jenkins agent communication
If you plan to connect remote agents, you’ll need to open 50000/TCP in your panel’s port allocation.


📦 Environment Variables
Variable	Default	Description
SERVER_JARFILE	jenkins.war	File used to start Jenkins
SERVER_PORT	8123	Port Jenkins binds to
JAVA_OPTS	-Djava.util.prefs.userRoot=/tmp	JVM preferences fix
JENKINS_ARGS	--httpListenAddress=0.0.0.0	Ensures access from outside


🧰 Dependencies

Required on the host node:

Docker ≥ 24.x

Wings ≥ 1.11.x

Panel ≥ 1.11.x

CPU architecture: amd64 or arm64

Internet connectivity to get.jenkins.io for first-time installation

🚦 Common Issues
Symptom	Cause	Fix
install: cannot obtain installation lock	Stuck _installer container	sudo docker rm -f $(docker ps -aq -f name=_installer) then restart Wings
Couldn't create user preferences directory	JVM prefs issue	Already fixed by -Djava.util.prefs.userRoot=/tmp
404 while downloading jenkins.war	Jenkins mirror redirect	The script retries using fallback URL
Web page won’t load	Port/firewall issue	Ensure the allocated port (8123) is open to your IP and assigned in the panel

🌍 Accessing Jenkins

After installation and startup:

Visit http://<YOUR_NODE_IP>:<ALLOCATED_PORT>
Example: http://192.168.0.243:8123

Initial setup wizard will prompt you to unlock Jenkins:
cat /mnt/server/jenkins_home/secrets/initialAdminPassword

🧩 Credits

Jenkins Project


---

Would you like me to include a **“Quick Install” shell section** at the top (copy-paste for end-users), e.g.  
```bash
bash <(curl -s https://raw.githubusercontent.com/augesrob/ptero-jenkins/main/install.sh)
````
