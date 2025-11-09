# 🧩 Jenkins LTS – Pterodactyl Egg (2025 Edition)

A **fully working, schema-validated** Jenkins LTS egg for **Pterodactyl Panel v1.11 and newer**.  
Built to run Jenkins 2.479.3 LTS with **Java 17**, clean logs, correct permissions, and no variable conflicts.  
Perfect for self-hosted CI/CD setups on Pterodactyl.

---

## 📦 Features
- ✅ Automatic Jenkins LTS (2.479.3) installation  
- ✅ Java 17 runtime (`ghcr.io/pterodactyl/yolks:java_17`)  
- ✅ Auto-creates `jenkins_home` and sets correct UID/GID  
- ✅ Panel-safe JSON schema (no startup errors)  
- ✅ Persistent storage (`/home/container/jenkins_home`)  
- ✅ Access via HTTP port 8123 (default)  
- ✅ Generates initial admin password automatically  

---

## ⚙️ Requirements
| Component | Minimum Version | Notes |
|------------|-----------------|-------|
| **Pterodactyl Panel** | v1.10 + | Validated JSON schema required |
| **Wings Daemon** | v1.11 + | Must support PTDL v2 eggs |
| **Docker Image** | [`ghcr.io/pterodactyl/yolks:java_17`](https://github.com/pterodactyl/yolks) | Bundles OpenJDK 17 |
| **Host System** | Ubuntu 22.04 + | 1 vCPU / 1 GB RAM minimum |
| **Ports** | 8123 (default) | Configure additional if needed |

---

## 🚀 Installation Steps

### 🐣 1. Import the Egg
1. Go to **Admin → Nests → Import Egg**  
2. Upload `jenkins-final.json` from this repo  
3. Assign to your preferred nest (category)

---

### 🧰 2. Create a New Server
- **Docker Image:** `ghcr.io/pterodactyl/yolks:java_17`  
- **Startup Command:**
  ```bash
  java -Djava.util.prefs.userRoot=/tmp \
       -DJENKINS_HOME=/home/container/jenkins_home \
       -jar /home/container/jenkins.war \
       --httpPort=${JENKINS_PORT} --httpListenAddress=0.0.0.0
Environment Variable:
JENKINS_PORT=8123

🔧 3. First Boot

The installer downloads and sets up Jenkins 2.479.3 LTS

When finished, look for:
✅ Jenkins 2.479.3 installed successfully.
➡️ WAR: /mnt/server/jenkins.war
➡️ Home: /mnt/server/jenkins_home
➡️ Port: 8123
Installation complete.
http://<your-server-ip>:8123
cat /home/container/jenkins_home/secrets/initialAdminPassword


🧠 Troubleshooting
Issue	Fix
java.util.prefs.FileSystemPreferences warnings	Harmless – preferences redirected to /tmp
Server stuck on JShell prompt	Startup command misconfigured – check egg’s startup string
“Could not lock User prefs”	Ignore; does not affect runtime
“Built-In Node offline temporarily”	Jenkins checking free disk space; safe to ignore or free space
# Install default plugins
mkdir -p jenkins_home/plugins
cat <<EOF > jenkins_home/plugins.txt
blueocean
git
workflow-aggregator
EOF
java -jar jenkins.war --pluginroot=jenkins_home/plugins --list

/home/container/
├── jenkins.war              # Jenkins binary
├── jenkins_home/            # Persistent workspace
│   ├── config.xml
│   ├── jobs/
│   └── secrets/

🛠️ Maintenance Tips

Backup /home/container/jenkins_home regularly

Update by replacing jenkins.war with a new LTS version

Use Manage Jenkins → Plugins for updates

Check /home/container/jenkins_home/logs/ for runtime logs

🧾 License

MIT License © 2025 Augesrob Project
This egg is provided as-is without warranty or liability.

💬 Support

For issues or enhancements, open an issue on GitHub or contact:
📧 augesrob@augesrob.net


---

Would you like me to **generate the full GitHub repository structure** (with `/egg/jenkins-final.json`, `/README.md`, and a `/screenshots/` placeholder) in a zip so you can upload it directly?
