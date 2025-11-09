# Security Policy

## 🔐 Sicherheit bei Darkmatter IT Storage Scripts

Sicherheit ist für uns sehr wichtig. Dieses Dokument beschreibt unsere Sicherheitsmaßnahmen und wie du sicher mit diesen Scripts arbeitest.

---

## ⚠️ Wichtige Warnungen

### Root-Zugriff erforderlich

Diese Scripts erfordern **root/sudo Zugriff**. Das bedeutet:

- ✅ **DO**: Nutze `sudo ./script.sh` zum Ausführen
- ⚠️ **BE CAREFUL**: Überprüfe Commands immer vor Ausführung
- ❌ **DON'T**: Scripts automatisiert ohne Überprüfung ausführen
- ❌ **DON'T**: Unkontrollierter Zugriff für andere Benutzer

### Datenverlust ist möglich

Diese Scripts können **Daten permanent löschen**:

```bash
# ❌ GEFÄHRLICH: Löscht ALLES auf der Disk!
./mount-size.sh --wipe-disk -d /dev/sdc

# ❌ GEFÄHRLICH: Löscht komplette VG!
./lvm-manage.sh delete-vg -vg backup-pool
```

**Immer Backups machen** bevor du Operationen ausführst!

---

## 🛡️ Best Practices

### 1. Umgebung absichern

```bash
# Überprüfe verfügbare Disks
lsblk

# Überprüfe aktuelle Mounts
mount

# Überprüfe Device-Namen
ls -la /dev/sd*
```

### 2. Device-Namen IMMER überprüfen

```bash
# ✅ SICHER: Überprüfe Disk vorher
sudo lsblk
# Output zeigt welche Disk welche Größe hat
# Nur dann ausführen wenn du die Disk identifiziert hast

sudo ./mount-size.sh -m /part/data -d /dev/sdc -s 1000G

# ❌ UNSICHER: Blindlings ausführen
sudo ./mount-size.sh -m /part/data -d /dev/sdc -s 1000G
```

### 3. Trockentest durchführen

```bash
# Test auf TEST-System zuerst
sudo ./mount-size.sh -h
sudo ./lvm-manage.sh -h

# Dann auf Production nach gründlicher Überprüfung
```

### 4. Automatisierung absichern

```bash
# ✅ SICHER: Mit Bestätigung
*/0 2 * * * /root/check-disk.sh | mail -s "Daily Report" admin@example.com

# ❌ UNSICHER: Destruktive Befehle automatisiert
*/0 2 * * * sudo ./mount-size.sh --wipe-disk -d /dev/sdc
```

---

## 📋 Input Validation

Die Scripts validieren folgende Eingaben:

### mount-size.sh
- ✅ Device existiert (`-d /dev/sdc`)
- ✅ Größenformat valid (`-s 1000G`)
- ✅ Mountpoint nicht duplikat
- ✅ Dateisystem unterstützt

### lvm-manage.sh
- ✅ VG/LV Namen valid
- ✅ Größen logisch
- ✅ UUIDs eindeutig
- ✅ Pfade sicher

---

## 🔑 Credentials & Secrets

### Was NIEMALS commiten:

```
❌ SSH Keys (id_rsa, *.pem)
❌ API Keys
❌ Passwörter
❌ Tokens
❌ Private Keys
```

### Sichere Verwaltung:

```bash
# ✅ SICHER: Environment Variables
export SSH_KEY_PATH="/root/.ssh/id_rsa"

# ✅ SICHER: File mit 600 Permissions
sudo chmod 600 /root/.ssh/id_rsa

# ❌ UNSICHER: Credentials im Script
SSH_KEY="my-super-secret-key"
```

---

## 🔍 Audit & Logging

### Aktivitäten loggen

```bash
# Überwache wer Commands ausführt
sudo su - -c "history | grep mount-size.sh"

# oder nutze auditd
sudo auditctl -w /usr/local/bin/mount-size -p x
```

### Wichtige Logs überprüfen

```bash
# System Logs
sudo journalctl -u mount-size.sh

# Disk Operations
sudo lsof

# LVM Events
sudo lvmdiskscan -l
```

---

## 🚨 Notfall-Verfahren

### Wenn etwas schiefgeht

```bash
# 1. SOFORT STOPPEN - Ctrl+C wenn noch läuft
# 2. Überprüfe aktuelle Zustand
lsblk
df -h
sudo lvdisplay

# 3. Kontaktiere das Support-Team
# 4. Stelle detaillierte Logs bereit
journalctl -n 100
dmesg | tail -50

# 5. Führe Notfall-Backup durch
sudo tar -czf emergency-backup-$(date +%s).tar.gz /important/data/
```

---

## 📞 Security Vulnerabilities melden

### Verantwortungsvolle Offenlegung

**KEINE Public Issues für Security Vulnerabilities!**

Stattdessen:

1. Email an: **security@darkmatter-it.de**
2. Betreff: `[SECURITY] Vulnerability Report`
3. Detaillierte Beschreibung
4. Reproduzierungsschritte
5. Vorschlag für Fix (optional)

### Antwortzeiten

- ⚠️ **Critical**: 24 Stunden
- 🔴 **High**: 48 Stunden
- 🟡 **Medium**: 1 Woche
- 🟢 **Low**: 2 Wochen

---

## 🔐 Code Review

Alle PRs werden auf Sicherheit überprüft:

- [ ] Keine Secrets in Code
- [ ] Input Validation vorhanden
- [ ] Error Handling korrekt
- [ ] Keine Privilege Escalation
- [ ] Keine Shell Injections
- [ ] Sichere File Permissions

---

## 📚 Sichere Verwendung

### Checkliste für Production

- [ ] Scripts gründlich getestet
- [ ] Backups erstellt
- [ ] Device-Namen überprüft
- [ ] Nur autorisierte Benutzer haben Zugriff
- [ ] Logging aktiviert
- [ ] Notfall-Plan vorhanden
- [ ] Team informiert

### Operationalisierung

```bash
# 1. Dokumentiere alles
echo "Creating LV for customer-xyz at $(date)" >> /var/log/operations.log

# 2. Überprüfe Zustand vorher
lsblk > /tmp/before-state.txt
vgdisplay >> /tmp/before-state.txt

# 3. Führe Operation durch
sudo ./mount-size.sh -m /part/data -d /dev/sdc -s 1000G

# 4. Überprüfe Zustand nachher
lsblk > /tmp/after-state.txt
vgdisplay >> /tmp/after-state.txt

# 5. Vergleiche
diff /tmp/before-state.txt /tmp/after-state.txt
```

---

## 🛠️ Systemhärtung

### Minimale Sicherheitsschritte

```bash
# 1. Stelle sicher dass nur root Scripts ausführen kann
sudo chown root:root /usr/local/bin/mount-size
sudo chmod 700 /usr/local/bin/mount-size

# 2. Überprüfe sudo Konfiguration
sudo visudo
# Nur vertrauenswürdige Benutzer sollten sudo haben

# 3. Aktiviere Logging
sudo nano /etc/sudoers
# Füge hinzu: Defaults use_pty,log_output

# 4. Setze File Permissions
sudo chmod 640 /var/log/auth.log
```

---

## 🔄 Updates & Patches

### Sicherheits-Updates

```bash
# Überprüfe auf Updates
git pull origin main

# Review Changelog
cat CHANGELOG.md

# Test auf Staging
sudo ./mount-size.sh -h

# Deploy auf Production
sudo cp mount-size.sh /usr/local/bin/mount-size
```

### Dependencies aktualisieren

```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get upgrade

# Installiere Security Updates
sudo apt-get install --only-upgrade linux-image
```

---

## 📋 Compliance

### Empfohlene Standards

- ✅ NIST Cybersecurity Framework
- ✅ ISO 27001
- ✅ CIS Benchmarks
- ✅ GDPR (falls zutreffend)

### Dokumentation für Compliance

```
/docs/security/
├── Security Policy (dieses Dokument)
├── Audit Logs
├── Backup Verification
├── Access Control
└── Incident Reports
```

---

## 🙋 Fragen zur Sicherheit?

Kontaktiere das Security Team:

- 📧 Email: **hi@darkmatter-it.de**
- 🔐 PGP Key: [Available on request]
- 📞 Support: hi@darkmatter-it.de

---

## 📜 Disclaimer

Diese Scripts werden bereitgestellt "as is" ohne Garantien. Nutze auf eigenes Risiko. 

**Du bist verantwortlich für:**
- Backups deiner Daten
- Sichere Konfiguration
- Monitoring und Audit
- Compliance mit lokalen Gesetzen

---

**Stay Secure! 🔐**
