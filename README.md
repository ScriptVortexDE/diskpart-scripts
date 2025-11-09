# Darkmatter IT - Linux Storage Management Scripts

<div align="center">

![Version](https://img.shields.io/badge/version-1.0-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)
![Linux](https://img.shields.io/badge/Linux-Ubuntu%20%2F%20Debian-orange.svg)
![Status](https://img.shields.io/badge/status-Production%20Ready-brightgreen.svg)

**Professional Linux storage management tools for PBS backup systems and flexible partition management**

[Features](#-features) • [Installation](#-installation) • [Quick Start](#-quick-start) • [Documentation](#-documentation) • [Scripts](#-scripts)

</div>

---

## 🎯 Overview

**Darkmatter IT Storage Scripts** sind professionelle Linux-Tools für die Verwaltung von Speicherpartitionen und Logical Volumes. Speziell entwickelt für Proxmox Backup Server (PBS) und flexible Kundenverwaltung.

Diese Collection enthält zwei zentrale Scripts:
- **mount-size.sh** - GPT Partitionsverwaltung (einfach & schnell)
- **lvm-manage.sh** - LVM Volume Management (flexibel & skalierbar)

---

## ✨ Features

### 🔧 mount-size.sh
- ✅ GPT Partitionserstellung mit spezifischen Größen
- ✅ Automatisches Formatieren (ext4, xfs, btrfs)
- ✅ Automatisches Mounten & fstab Integration
- ✅ Disk-Wipe Funktionen (schnell & sicher)
- ✅ Mehrere Partitionen pro Disk
- ✅ Einfache Bedienung

### 📦 lvm-manage.sh
- ✅ Flexible Logical Volume Verwaltung
- ✅ Volumes vergrößern/verkleinern ohne Ausfallzeit
- ✅ Automatisches Tracking
- ✅ UUID-basierte fstab Integration
- ✅ Detaillierte Statistiken & Monitoring
- ✅ Sicheres Löschen mit Bestätigung
- ✅ Volume Group Expansion

### 🌟 Gemeinsame Features
- 🔐 Root-Authentifizierung erforderlich
- 📊 Detailliertes Logging & Feedback
- 🎨 Farbcodierte Ausgabe
- ⚡ Production-Ready Code
- 📝 Umfassende Dokumentation

---

## 🚀 Installation

### Voraussetzungen
```bash
# Linux OS (Ubuntu/Debian empfohlen)
# Root oder sudo Zugriff erforderlich
# util-linux, e2fsprogs, parted (für mount-size.sh)
# lvm2 (für lvm-manage.sh)
```

### Installation von GitHub
```bash
# Repository klonen
git clone https://github.com/yourusername/darkmatter-storage-scripts.git
cd darkmatter-storage-scripts

# Scripts ausführbar machen
chmod +x mount-size.sh
chmod +x lvm-manage.sh

# Optional: In System-Pfad kopieren
sudo cp mount-size.sh /usr/local/bin/mount-size
sudo cp lvm-manage.sh /usr/local/bin/lvm-manage
sudo chmod +x /usr/local/bin/mount-size /usr/local/bin/lvm-manage
```

---

## ⚡ Quick Start

### mount-size.sh - Partitionierung

**Neue Disk mit Partitionen einrichten:**
```bash
# Partition 1: 1000GB
sudo ./mount-size.sh -m /part/data1 -d /dev/sdc -s 1000G

# Partition 2: 500GB
sudo ./mount-size.sh -m /part/data2 -d /dev/sdc -s 500G

# Status überprüfen
lsblk /dev/sdc
df -h /part/data*
```

**Disk komplett löschen:**
```bash
# Alle Partitionen löschen und neu initialisieren
sudo ./mount-size.sh --wipe-disk -d /dev/sdc
```

---

### lvm-manage.sh - LVM Management

**Volume Group & Volumes erstellen:**
```bash
# VG erstellen (einmalig)
sudo ./lvm-manage.sh create-vg -d /dev/sde -vg backup-pool

# LV für Kunde erstellen (1000GB)
sudo ./lvm-manage.sh create-lv -vg backup-pool -lv kunde1 -s 1000G -m /backup/kunde1

# Status überprüfen
sudo ./lvm-manage.sh status --vgn backup-pool
sudo ./lvm-manage.sh stats -vg backup-pool
```

**Bestehendes Volume vergrößern:**
```bash
# Von 1000G auf 1500G
sudo ./lvm-manage.sh resize-lv -vg backup-pool -lv kunde1 -s 1500G

# Überprüfung
df -h /backup/kunde1
```

---

## 📚 Documentation

Detaillierte Dokumentation für jedes Script:

### mount-size.sh
```
mount-size/
├── README.md              # Vollständige Dokumentation
├── EXAMPLES.md           # Praktische Beispiele
└── TROUBLESHOOTING.md    # Fehlerbehandlung
```

[📖 mount-size.sh Dokumentation →](./mount-size/README.md)

### lvm-manage.sh
```
lvm-manage/
├── README.md              # Vollständige Dokumentation
├── EXAMPLES.md           # Praktische Beispiele
└── TROUBLESHOOTING.md    # Fehlerbehandlung
```

[📖 lvm-manage.sh Dokumentation →](./lvm-manage/README.md)

---

## 📖 Scripts

### 🔧 [mount-size.sh](./mount-size/)

**Zweck:** Einfache, schnelle Partitionserstellung auf großen Disks (>2TB)

**Beste Verwendung für:**
- Schnelle Disk-Partitionierung
- Einfache Speicher-Strukturen
- Test & Entwicklungsumgebungen
- One-Time Setup Szenarien

**Syntax:**
```bash
./mount-size.sh -m <mountpoint> -d <device> -s <size> [-t <fstype>]
```

[Dokumentation →](./mount-size/README.md) | [Beispiele →](./mount-size/EXAMPLES.md)

---

### 📦 [lvm-manage.sh](./lvm-manage/)

**Zweck:** Flexible, skalierbare Logical Volume Verwaltung mit Resizing

**Beste Verwendung für:**
- Kundenspezifische Speicher-Quotas
- Häufige Größen-Änderungen
- Enterprise Backup-Systeme
- Production Umgebungen

**Syntax:**
```bash
./lvm-manage.sh <command> -vg <vg-name> [-lv <lv-name>] [-s <size>] [-m <mountpoint>]
```

[Dokumentation →](./lvm-manage/README.md) | [Beispiele →](./lvm-manage/EXAMPLES.md)

---

## 🔄 Vergleich

| Feature | mount-size.sh | lvm-manage.sh |
|---------|---------------|---------------|
| **Komplexität** | ⭐ Einfach | ⭐⭐ Mittel |
| **Resizable** | ❌ Nein | ✅ Ja |
| **Partition-Limit** | ~10-20 | Unbegrenzt |
| **Setup-Geschwindigkeit** | ⚡ Schnell | ⚡⚡ Mittel |
| **Production Ready** | ✅ Ja | ✅ Ja |
| **Best For** | Einfache Setups | Flexible Verwaltung |

---

## 💼 Use Cases

### 1️⃣ Einfaches Backup-System
```
→ mount-size.sh verwenden
- 3-5 große Partitionen
- Statische Größen
- Minimale Verwaltung
```

### 2️⃣ Professionelles PBS System
```
→ lvm-manage.sh verwenden
- Viele Kunden
- Dynamische Größen-Änderungen
- Automatisches Tracking
```

### 3️⃣ Hybrid-Setup
```
→ Beide Scripts kombinieren
- mount-size.sh für Grundstruktur
- lvm-manage.sh für Kundenverwaltung
- Beste Flexibilität
```

---

## 🛠️ Anforderungen

### System
- Linux OS (Ubuntu 20.04+, Debian 10+)
- Root oder sudo Zugriff
- Mindestens 4GB RAM

### Für mount-size.sh
```bash
sudo apt-get install util-linux e2fsprogs parted
```

### Für lvm-manage.sh
```bash
sudo apt-get install lvm2 util-linux e2fsprogs
```

---

## 📝 Beispiele

### Schnelle Übersicht

**Neue 12TB Disk mit 3 Partitionen (mount-size.sh):**
```bash
sudo ./mount-size.sh -m /part/tier1 -d /dev/sdc -s 4000G
sudo ./mount-size.sh -m /part/tier2 -d /dev/sdc -s 4000G
sudo ./mount-size.sh -m /part/tier3 -d /dev/sdc -s 2000G
```

**Flexible Kunden-Verwaltung (lvm-manage.sh):**
```bash
# Setup einmalig
sudo ./lvm-manage.sh create-vg -d /dev/sde -vg backup-pool

# Customers hinzufügen
sudo ./lvm-manage.sh create-lv -vg backup-pool -lv customer-01 -s 500G -m /backup/customer-01
sudo ./lvm-manage.sh create-lv -vg backup-pool -lv customer-02 -s 1000G -m /backup/customer-02

# Upgrade wenn nötig
sudo ./lvm-manage.sh resize-lv -vg backup-pool -lv customer-01 -s 750G
```

[Mehr Beispiele →](./mount-size/EXAMPLES.md) | [Weitere Beispiele →](./lvm-manage/EXAMPLES.md)

---

## 🔐 Sicherheit

### ⚠️ Wichtig

- **Immer Backups machen** vor Disk-Operationen
- **Device-Namen überprüfen** bevor Sie Befehle ausführen
- **Root-Zugriff erforderlich** - Vorsicht bei Automatisierung
- **Regelmäßig Speicher überprüfen** um Überläufe zu vermeiden

### Best Practices

```bash
# 1. Device überprüfen
lsblk

# 2. Backup erstellen
tar -czf backup-$(date +%Y%m%d).tar.gz /your/data/

# 3. Script ausführen
sudo ./mount-size.sh -m /mount/point -d /dev/sdc -s 1000G

# 4. Überprüfung
lsblk
df -h
```

---

## 🆘 Troubleshooting

### Häufige Probleme

**"Device nicht gefunden"**
```bash
# Überprüfe verfügbare Devices
lsblk
ls -la /dev/sd*
```

**"Permission denied"**
```bash
# Scripts müssen mit sudo aufgerufen werden
sudo ./mount-size.sh ...
```

**"Mount fehlgeschlagen"**
```bash
# Überprüfe ob Mountpoint existiert
sudo mkdir -p /mount/point

# Oder überprüfe fstab
cat /etc/fstab
```

[Vollständiges Troubleshooting →](./mount-size/TROUBLESHOOTING.md)

---

## 📊 Monitoring & Logs

### Live-Übersicht
```bash
# Speichernutzung
df -h

# Partitionen/Volumes
lsblk

# LVM Status
sudo lvdisplay
sudo vgdisplay

# Statistics (lvm-manage.sh)
sudo ./lvm-manage.sh stats -vg backup-pool
```

### Automatische Überwachung
```bash
# Tägliche Checks per Cron
0 2 * * * df -h >> /var/log/disk-check.log
0 2 * * * lsblk >> /var/log/partition-check.log
```

---

## 📞 Support & Hilfe

### Commands
```bash
# Hilfe anzeigen
./mount-size.sh -h
./lvm-manage.sh -h

# Version
./mount-size.sh --version
./lvm-manage.sh --version
```

### Dokumentation
- [mount-size.sh README](./mount-size/README.md)
- [lvm-manage.sh README](./lvm-manage/README.md)
- [Troubleshooting Guides](./mount-size/TROUBLESHOOTING.md)

---

## 🤝 Contributing

Beiträge sind willkommen! Bitte:

1. Fork das Repository
2. Feature Branch erstellen (`git checkout -b feature/AmazingFeature`)
3. Änderungen committen (`git commit -m 'Add some AmazingFeature'`)
4. Branch pushen (`git push origin feature/AmazingFeature`)
5. Pull Request öffnen

---

## 📄 License

Diese Scripts sind unter der **MIT License** lizenziert. Siehe [LICENSE](./LICENSE) für Details.

---

## 📈 Roadmap

### Geplante Features
- [ ] Web-Dashboard
- [ ] WHMCS Integration
- [ ] Automatische Backups
- [ ] Alert System (80% Auslastung)
- [ ] Docker Support
- [ ] Terraform Modules

---

## 👨‍💼 Über Darkmatter IT

**Darkmatter IT** - Professionelle IT-Services & Infrastruktur

- 🌍 Website: https://darkmatter-it.de
- 📧 Email: info@darkmatter-it.de
- 🔐 Speicher & Backup Lösungen
- 🖥️ Managed Hosting Services

---

## 📝 Changelog

### v1.0 (2025-01-XX)
- ✅ Initiale Veröffentlichung
- ✅ mount-size.sh vollständig
- ✅ lvm-manage.sh vollständig
- ✅ Umfassende Dokumentation
- ✅ Troubleshooting Guides

---

## ⭐ Gefällt dir das Projekt?

Wenn diese Scripts dir geholfen haben, gib uns einen **Star** ⭐ auf GitHub!

```bash
# Oder unterstütze uns durch Feedback & Issues
# https://github.com/yourusername/darkmatter-storage-scripts/issues
```

---

<div align="center">

**Made with ❤️ by Darkmatter IT**

[⬆ Back to top](#darkmatter-it---linux-storage-management-scripts)

</div>
