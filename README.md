
# 🛠️ DevOps(Linux) Hands-On Practice

This document contains real-world mini-projects to practice **Linux + Automation + DevOps Skills**.  
Each project includes an outline, concept explanation, and example script. 🚀

---

## 1️⃣ Web Server Automation 🌐

**Goal:** Automate setting up Nginx and opening port 80.

**Concepts practiced:**
- Package management (`apt`, `yum`)
- Service management (`systemctl`)
- Firewall (`ufw`)

**Example Script:**
```bash
#!/usr/bin/env bash
set -e

echo "🚀 Installing Nginx..."
sudo apt update -y
sudo apt install -y nginx

echo "✅ Enabling and starting Nginx..."
sudo systemctl enable nginx
sudo systemctl start nginx

echo "🔒 Configuring firewall..."
sudo ufw allow 80/tcp
sudo ufw reload

echo "🎉 Nginx is up! Check with: curl -I http://localhost"
```

---

## 2️⃣ Log Rotation 📝

**Goal:** Move yesterday’s logs to `/archive` with a timestamp.

**Concepts practiced:**
- Log management
- Timestamps in bash
- Archiving/compressing

**Example Script:**
```bash
#!/usr/bin/env bash
set -e

LOG_DIR="/var/log/nginx"
ARCHIVE_DIR="/archive"
TODAY=$(date +%F)

mkdir -p "$ARCHIVE_DIR"

for file in "$LOG_DIR"/*.log; do
  BASENAME=$(basename "$file")
  NEWFILE="${ARCHIVE_DIR}/${BASENAME}.${TODAY}.gz"

  echo "📦 Archiving $file -> $NEWFILE"
  sudo cp "$file" "$NEWFILE"
  sudo gzip -f "$NEWFILE"
  sudo truncate -s 0 "$file"
done

echo "✅ Logs archived successfully!"
```

---

## 3️⃣ User Management 👥

**Goal:** Bulk-create users from a file.

**Concepts practiced:**
- User accounts & permissions
- File parsing in bash
- Secure password handling

**Input file (`users.txt`):**
```
Abhi
Sachin
Ravi
```

**Example Script:**
```bash
#!/usr/bin/env bash
set -e

FILE="users.txt"

while IFS= read -r user; do
  if id "$user" &>/dev/null; then
    echo "⚠️ User $user already exists, skipping..."
  else
    echo "👤 Creating user: $user"
    sudo useradd -m -s /bin/bash "$user"
    echo "$user:Password@123" | sudo chpasswd
    sudo passwd -e "$user"
  fi
done < "$FILE"

echo "✅ All users processed."
```

---

## 4️⃣ Health Check 🔥

**Goal:** Check if Nginx is running, restart if not.

**Concepts practiced:**
- Process/service monitoring
- Self-healing automation
- Exit codes

**Example Script:**
```bash
#!/usr/bin/env bash

SERVICE="nginx"

if systemctl is-active --quiet "$SERVICE"; then
  echo "✅ $SERVICE is running."
else
  echo "❌ $SERVICE is down! Restarting..."
  sudo systemctl restart "$SERVICE"
  sleep 2
  if systemctl is-active --quiet "$SERVICE"; then
    echo "🚀 $SERVICE restarted successfully."
  else
    echo "🔥 Failed to restart $SERVICE!"
    exit 1
  fi
fi
```

