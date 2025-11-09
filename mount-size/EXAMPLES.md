# mount-size.sh - Praktische Beispiele

Detaillierte Beispiele für verschiedene Anwendungsszenarien.

---

## 🎯 Basis Beispiele

### Beispiel 1: Einfache Partitionierung einer neuen Disk

**Szenario:** Neue 4TB Disk installiert, soll partitioniert werden

```bash
# 1. Überprüfe verfügbare Disks
lsblk

# Output:
# sdc           8:32    0   4T  0 disk

# 2. Erstelle erste Partition (2TB)
sudo ./mount-size.sh -m /part/data1 -d /dev/sdc -s 2000G

# 3. Erstelle zweite Partition (2TB)
sudo ./mount-size.sh -m /part/data2 -d /dev/sdc -s 2000G

# 4. Überprüfe Ergebnis
lsblk /dev/sdc
df -h /part/data*
```

**Ergebnis:**
```
/dev/sdc1  2000G  2.1M  2000G   1% /part/data1
/dev/sdc2  2000G  2.1M  2000G   1% /part/data2
```

---

### Beispiel 2: Verschiedene Partitionsgrößen

**Szenario:** 12TB Disk mit 3 verschiedenen Größen

```bash
# Große Partition (5TB)
sudo ./mount-size.sh -m /part/backups -d /dev/sdd -s 5000G

# Mittlere Partition (4TB)
sudo ./mount-size.sh -m /part/storage -d /dev/sdd -s 4000G

# Kleine Partition (3TB)
sudo ./mount-size.sh -m /part/archive -d /dev/sdd -s 3000G

# Überblick
lsblk /dev/sdd
```

---

### Beispiel 3: Verschiedene Dateisysteme

**Szenario:** Verschiedene Workloads brauchen verschiedene Filesysteme

```bash
# ext4 für Standard (empfohlen für meisten Fälle)
sudo ./mount-size.sh -m /part/standard -d /dev/sde -s 2000G -t ext4

# XFS für große Dateien (Backups, Videos)
sudo ./mount-size.sh -m /part/big-files -d /dev/sde -s 3000G -t xfs

# btrfs für Snapshots (experimentell)
sudo ./mount-size.sh -m /part/snapshots -d /dev/sde -s 2000G -t btrfs

# Überprüfe Dateisysteme
df -T /part/*
```

---

## 💼 Production Beispiele

### Beispiel 4: Backup-System mit mehreren Tier-Ebenen

**Szenario:** 3 Backup-Server mit unterschiedlichen Zwecken

```bash
# Server 1: Hot Backups (schneller Zugriff)
sudo ./mount-size.sh -m /backups/hot -d /dev/sdc -s 2000G -t ext4

# Server 2: Warm Backups (mittlerer Zugriff)
sudo ./mount-size.sh -m /backups/warm -d /dev/sdd -s 5000G -t xfs

# Server 3: Cold Backups (Langzeitspeicherung)
sudo ./mount-size.sh -m /backups/cold -d /dev/sde -s 10000G -t ext4

# Verteile Last auf verschiedene Disks
# Hot: SSD für schnelle Zugriffe
# Warm: SATA HDD für Balance
# Cold: Large SATA HDD für Archiv
```

**Monitoring Script:**
```bash
#!/bin/bash
echo "Backup Tier Status:"
for tier in hot warm cold; do
    echo ""
    echo "=== $tier ==="
    df -h /backups/$tier/
done
```

---

### Beispiel 5: Mehrere Kunden auf einer Disk

**Szenario:** Mehrere Kunden teilen sich eine große Disk

```bash
# Setup für 4 Kunden auf 12TB Disk
sudo ./mount-size.sh -m /customers/kunde-a -d /dev/sdf -s 2500G
sudo ./mount-size.sh -m /customers/kunde-b -d /dev/sdf -s 3000G
sudo ./mount-size.sh -m /customers/kunde-c -d /dev/sdf -s 3500G
sudo ./mount-size.sh -m /customers/kunde-d -d /dev/sdf -s 2000G

# Struktur:
# /customers/
# ├── kunde-a/     (2.5TB)
# ├── kunde-b/     (3TB)
# ├── kunde-c/     (3.5TB)
# └── kunde-d/     (2TB)

# Monitoring Script:
for customer in a b c d; do
    echo "Kunde $customer:"
    du -sh /customers/kunde-$customer/
done
```

---

### Beispiel 6: Disk-Erweiterung

**Szenario:** Alte Disk voll, neue Disk hinzugefügt

```bash
# Alte Disk Status (voll)
lsblk /dev/sdc
# /dev/sdc1  4000G (voll)

# Neue Disk vorhanden
lsblk /dev/sdd
# /dev/sdd (leer, 4TB)

# Neue Partitionen auf neuer Disk erstellen
sudo ./mount-size.sh -m /backups/pool2 -d /dev/sdd -s 4000G

# Jetzt haben wir 8TB statt 4TB
du -sh /backups/pool1 /backups/pool2
# 4.0T /backups/pool1 (alte Disk)
# 4.0T /backups/pool2 (neue Disk)
```

---

## 🧹 Maintenance Beispiele

### Beispiel 7: Disk komplett neu formatieren

**Szenario:** Alte Disk recyclen für neuen Zweck

```bash
# 1. Prüfe welche Disk es ist
lsblk

# 2. Backup machen (falls nötig)
sudo tar -czf /backup/backup-sdc-$(date +%Y%m%d).tar.gz /important/data/

# 3. Unmounte alles auf der Disk
sudo umount /part/data1
sudo umount /part/data2

# 4. Wipe die Disk
sudo ./mount-size.sh --wipe-disk -d /dev/sdc

# Output:
# [INFO] Disk erfolgreich gelöscht und neu initialisiert!

# 5. Erstelle neue Partitionen
sudo ./mount-size.sh -m /data/pool1 -d /dev/sdc -s 2000G
sudo ./mount-size.sh -m /data/pool2 -d /dev/sdc -s 2000G

# Disk ist bereit für neuen Zweck!
```

---

### Beispiel 8: Sicheres Löschen (mit Nullen überschreiben)

**Szenario:** Disk wird ausrangiert, muss sicher gelöscht werden

```bash
# ⚠️ WARNUNG: Das dauert STUNDEN!

# 1. Überprüfe ob es die richtige Disk ist (3-4x überprüfen!)
lsblk
sudo fdisk -l /dev/sdc | head -5

# 2. Backup der Daten (falls nötig)
sudo tar -czf /backup/final-backup-sdc.tar.gz /important/

# 3. Starte sicheres Löschen
sudo ./mount-size.sh --wipe-all -d /dev/sdc

# Erwartet: 2-4 Stunden für 12TB

# 4. Warte bis fertig
# Output: Disk erfolgreich gelöscht und überschrieben!

# Disk ist jetzt vollständig sauber
```

---

## 📊 Monitoring & Reporting

### Beispiel 9: Automatisches Disk-Monitoring

**Szenario:** Tägliche Überwachung der Disk-Auslastung

```bash
#!/bin/bash
# File: /usr/local/bin/disk-report.sh

echo "=== Daily Disk Report ===" > /var/log/disk-report.txt
echo "Generated: $(date)" >> /var/log/disk-report.txt
echo "" >> /var/log/disk-report.txt

# Disk Overview
echo "Disk Partitions:" >> /var/log/disk-report.txt
lsblk >> /var/log/disk-report.txt
echo "" >> /var/log/disk-report.txt

# Storage Usage
echo "Storage Usage:" >> /var/log/disk-report.txt
df -h | grep "/part" >> /var/log/disk-report.txt
echo "" >> /var/log/disk-report.txt

# Disk Free Space Warning
echo "Disks with >80% Usage:" >> /var/log/disk-report.txt
df -h | grep "/part" | awk '$5 > 80 {print $0}' >> /var/log/disk-report.txt

# Email Report
mail -s "Daily Disk Report" admin@example.com < /var/log/disk-report.txt
```

**Cron Setup:**
```bash
# Run daily at 2 AM
0 2 * * * /usr/local/bin/disk-report.sh
```

---

### Beispiel 10: Disk-Vergleich vor/nach

**Szenario:** Dokumentiere Zustandsänderungen

```bash
#!/bin/bash
# Vor Operation
echo "=== BEFORE ===" > /tmp/disk-state.txt
lsblk >> /tmp/disk-state.txt
df -h >> /tmp/disk-state.txt

# Operation durchführen
sudo ./mount-size.sh -m /part/new -d /dev/sdc -s 1000G

# Nach Operation
echo "=== AFTER ===" >> /tmp/disk-state.txt
lsblk >> /tmp/disk-state.txt
df -h >> /tmp/disk-state.txt

# Vergleich anzeigen
diff <(head -20 /tmp/disk-state.txt | sed -n '1,/=== AFTER ===/p') \
     <(tail -20 /tmp/disk-state.txt)
```

---

## 🔧 Advanced Beispiele

### Beispiel 11: Batch-Partitionierung Script

**Szenario:** Automatische Partitionierung mehrerer Disks

```bash
#!/bin/bash
# File: batch-partition.sh
# Nutze: ./batch-partition.sh sdc 3000G 4

DEVICE=$1      # z.B. sdc
SIZE=$2        # z.B. 3000G
COUNT=$3       # z.B. 4 Partitionen

if [[ -z "$DEVICE" ]] || [[ -z "$SIZE" ]] || [[ -z "$COUNT" ]]; then
    echo "Usage: $0 <device> <size> <count>"
    echo "Example: $0 sdc 3000G 4"
    exit 1
fi

echo "Creating $COUNT partitions of $SIZE on /dev/$DEVICE"

for i in $(seq 1 $COUNT); do
    MOUNT="/part/data$i"
    echo "Creating partition $i: $MOUNT"
    sudo ./mount-size.sh -m $MOUNT -d /dev/$DEVICE -s $SIZE
    sleep 5  # Warte zwischen Partitionen
done

echo "Done!"
lsblk /dev/$DEVICE
```

**Verwendung:**
```bash
./batch-partition.sh sdc 3000G 4
# Erstellt 4 x 3000GB Partitionen auf /dev/sdc
```

---

### Beispiel 12: Disk-Gesundheitsprüfung

**Szenario:** Überprüfe Disk auf Fehler vor Verwendung

```bash
#!/bin/bash
# File: disk-health-check.sh

DEVICE=$1  # z.B. /dev/sdc

echo "=== Disk Health Check für $DEVICE ==="
echo ""

# 1. SMART Status (falls unterstützt)
echo "SMART Status:"
sudo smartctl -H $DEVICE 2>/dev/null || echo "SMART nicht unterstützt"
echo ""

# 2. Disk-Info
echo "Disk Information:"
sudo fdisk -l $DEVICE | head -10
echo ""

# 3. Read Test (lese erste 100MB)
echo "Read Test:"
sudo dd if=$DEVICE of=/dev/null bs=1M count=100 status=progress
echo "Read Test: OK"
echo ""

# 4. Partition Test (falls vorhanden)
echo "Partition Check:"
sudo fsck -n ${DEVICE}1 2>/dev/null || echo "Keine Partition gefunden"

echo ""
echo "Health Check Complete!"
```

---

## 📱 Mobil & Remote

### Beispiel 13: Remote Disk-Management

**Szenario:** Disk-Management über SSH

```bash
# Lokal auf Remote-Server
ssh root@backup-server << 'EOF'
# Remote Commands hier
./mount-size.sh -m /part/data -d /dev/sdc -s 2000G
lsblk /dev/sdc
df -h /part/data
EOF
```

---

### Beispiel 14: Automatisierter Backup per Cron

**Szenario:** Tägliche Partition-Prüfung per Cron

```bash
# In crontab
0 2 * * * /usr/local/bin/disk-check.sh >> /var/log/disk-check.log 2>&1

# File: /usr/local/bin/disk-check.sh
#!/bin/bash
echo "=== Disk Check $(date) ===" 
lsblk
df -h | grep "/part"

# Alert wenn >90% voll
df -h | grep "/part" | awk '$5 > 90 {system("echo Alert: " $0 " | mail admin@example.com")}'
```

---

## 🎓 Learning Beispiele

### Beispiel 15: Step-by-Step Lernen

```bash
# 1. START SIMPLE - Eine Partition
lsblk
sudo ./mount-size.sh -h  # Zeige Help
sudo ./mount-size.sh -m /part/test1 -d /dev/sdc -s 500G

# 2. Überprüfe Ergebnis
lsblk /dev/sdc
df -h /part/test1

# 3. Zweite Partition (unterschiedliche Größe)
sudo ./mount-size.sh -m /part/test2 -d /dev/sdc -s 1000G

# 4. Verschiedene Dateisysteme testen
sudo ./mount-size.sh -m /part/test-xfs -d /dev/sdc -s 750G -t xfs

# 5. Wipe & Neustart
sudo ./mount-size.sh --wipe-disk -d /dev/sdc

# Du verstehst jetzt wie's funktioniert!
```

---

## 💡 Tipps & Tricks

### Performance-Tuning
```bash
# Beste Performance Einstellungen
sudo ./mount-size.sh -m /part/fast -d /dev/sdc -s 1000G -t xfs
# XFS ist schneller für große Dateien

# Für viele kleine Dateien
sudo ./mount-size.sh -m /part/many -d /dev/sdc -s 1000G -t ext4
# ext4 bessere Kleine-Datei-Performance
```

### Sicherheit
```bash
# Vor kritischen Operationen immer:
# 1. Backup machen
tar -czf backup-$(date +%Y%m%d).tar.gz /important/

# 2. Device 2x überprüfen
lsblk
sudo fdisk -l /dev/sdc

# 3. Erst dann Operation durchführen
sudo ./mount-size.sh --wipe-disk -d /dev/sdc
```

---

**Weitere Fragen? Siehe [README.md](./README.md) oder [TROUBLESHOOTING.md](./TROUBLESHOOTING.md)**
