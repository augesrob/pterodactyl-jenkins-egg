# 🧩 Jenkins Pterodactyl Egg (Java 21 Stable LTS)

A ready-to-use **Pterodactyl Egg** for running **Jenkins LTS 2.x+** inside an isolated Docker environment using  
`ghcr.io/pterodactyl/yolks:java_21`.

Maintained by **Robert Augesen (@augesrob)**.

---

## ⚡ Quick Install

For users who prefer a one-line installation:
```bash
bash <(curl -s https://raw.githubusercontent.com/augesrob/ptero-jenkins/main/install.sh)
```

---

## 📦 Overview

Jenkins is a self-hosted automation server for building, testing, and deploying code.  
This egg installs **Jenkins LTS on Java 21**, fixes permissions for `/home/container/tmp`, and auto-creates `jenkins_home`.

✔ Fully compatible with Pterodactyl 1.11.11+  
✔ Automatic `tmp` and `jenkins_home` creation  
✔ Works with Temurin Java 21 runtime

---

## ⚙️ Server Specifications

| Setting | Recommended Value |
|:--|:--|
| **Base Docker Image** | `ghcr.io/pterodactyl/yolks:java_21` |
| **Startup Command** | `/bin/bash -c "mkdir -p /home/container/tmp /home/container/jenkins_home && chmod 777 /home/container/tmp /home/container/jenkins_home && java -Djava.io.tmpdir=/home/container/tmp -DJENKINS_HOME=/home/container/jenkins_home -jar /home/container/jenkins.war --httpPort=${JENKINS_PORT} --httpListenAddress=0.0.0.0"` |
| **Installer Base Image** | `ghcr.io/pterodactyl/installers:debian` |
| **Default Port** | 8123 |
| **Memory (RAM)** | 2 GB min / 4 GB recommended |
| **Disk** | ~2 GB base + 10 GB workspace |
| **CPU Cores** | 2+ |
| **Java Runtime** | OpenJDK 21 (Temurin JDK 21) |
| **Operating System** | Debian 12 (Bookworm) |

---

## 📂 File Structure

```
/mnt/server/
├── jenkins.war
├── jenkins_home/
├── tmp/
└── logs/
```

Data is persisted to Pterodactyl’s mounted volume.

---

## 🛠️ Installation Script Summary

The installer:

1. Updates apt sources  
2. Installs `wget ca-certificates openjdk-21-jre-headless`  
3. Creates `jenkins_home` and `tmp` with correct permissions  
4. Downloads latest LTS WAR (`https://get.jenkins.io/war-stable/latest/jenkins.war`)  
5. Falls back to `2.479.3` if mirror fails  
6. Outputs startup instructions and port info

---

## 🧠 Startup Details

Jenkins runs via:

```bash
java -Djava.io.tmpdir=/home/container/tmp -DJENKINS_HOME=/home/container/jenkins_home -jar /home/container/jenkins.war --httpPort=${JENKINS_PORT} --httpListenAddress=0.0.0.0
```

This fixes all “user preferences directory” errors.

---

### 🔒 Ports

| Port | Protocol | Purpose |
|:--|:--|:--|
| 8123 | TCP | Web dashboard |
| 50000 | TCP | (Optional) Agent communication |

---

### 🌿 Environment Variables

| Variable | Default | Description |
|:--|:--|:--|
| `JENKINS_PORT` | 8123 | Port Jenkins binds to |
| `JAVA_OPTS` | `-Djava.io.tmpdir=/home/container/tmp` | Temp directory fix |
| `JENKINS_HOME` | `/home/container/jenkins_home` | Persistent workspace |

---

## 🧰 Host Dependencies

- Docker ≥ 24.x  
- Wings ≥ 1.11.x  
- Panel ≥ 1.11.x  
- Architecture: amd64 / arm64  
- Internet access to `get.jenkins.io`

---

## 🚦 Common Issues & Fixes

| Symptom | Cause | Fix |
|:--|:--|:--|
| `install: cannot obtain installation lock` | Stuck installer container | `sudo docker rm -f $(docker ps -aq -f name=_installer)` then restart Wings |
| `Couldn't create user preferences directory` | Missing /tmp | Already fixed by egg’s startup command |
| `404 while downloading jenkins.war` | Mirror redirect | Installer uses fallback |
| `Web page won’t load` | Port not open | Open 8123 in Pterodactyl panel |
| `No such file /tmp` | Container permissions | Fixed automatically on startup |

---

## 🌍 Accessing Jenkins

Visit:  
`http://<NODE_IP>:8123`

Unlock with:
```bash
cat /mnt/server/jenkins_home/secrets/initialAdminPassword
```

---

## 🧩 Credits

- **Jenkins Project** for LTS releases  
- **Robert Augesen (@augesrob)** for Java 21 Pterodactyl integration  
- **Pterodactyl Community** for egg standards  

---
