#!/bin/bash

# ------------------------------
# Block Password Change Script
# Author: FarLax
# Purpose: Prevent all users from changing the VPS password
# ------------------------------

# Root check
if [ "$(id -u)" -ne 0 ]; then
  echo "[ERROR] Run this script as root or with sudo."
  exit 1
fi

echo "[+] Blocking password changes..."

# Backup original passwd binary if not already backed up
if [ -f /usr/bin/passwd ] && [ ! -f /usr/bin/passwd.real ]; then
  echo "[*] Backing up original /usr/bin/passwd..."
  mv /usr/bin/passwd /usr/bin/passwd.real
fi

# Create fake passwd script
echo "[*] Deploying fake /usr/bin/passwd script..."
cat > /usr/bin/passwd << 'EOF'
#!/bin/bash
LOG="/var/log/passwd_attempts.log"
echo "[!] $(date) - $(whoami) tried to change password from $(tty)" >> "$LOG"
echo "Password changing is disabled on this VPS."
exit 1
EOF

# Permissions and ownership
chmod 700 /usr/bin/passwd
chown root:root /usr/bin/passwd

# Make both files immutable
echo "[*] Locking files with chattr..."
chattr +i /usr/bin/passwd
chattr +i /usr/bin/passwd.real

echo "[✔] Password change is now blocked."
echo "[✔] Any attempt will be logged to /var/log/passwd_attempts.log"
