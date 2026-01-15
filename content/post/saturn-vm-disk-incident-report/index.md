---
title: "Saturn VM Disk Full and Container Failures"
date: 2026-01-14T11:42:00+10:00
categories:
    - Blog
tags: 
    - IT
    - Journal
---

# Journal & Report: Saturn VM Disk Full and Container Failures

**Date:** 2026-01-14  
**VM Name:** Saturn (VM-101)  
**Host:** Proxmox  
**Services Affected:** Home Assistant (HA), PostgreSQL, Plex Media Server  

---

## 1. Incident Overview

While attempting to deploy the Home Assistant stack via Portainer, several failures occurred:

- Home Assistant could not start because PostgreSQL exited with:
  ```
  FATAL: could not write lock file "postmaster.pid": No space left on device
  ```

- Plex Media Server crashed with SQLite disk I/O errors:
  ```
  sqlite3_statement_backend::prepare: disk I/O error for SQL: PRAGMA cache_size=2048
  ```

- VM console showed Out of Memory errors.

---

## 2. Investigation

Commands executed:

```
df -h
lsblk
vgdisplay
lvdisplay
```

Findings:

- Root filesystem (`/`) was completely full.
- VM disk in Proxmox was 210 GB, but the Linux logical volume was only 148 GB.
- LVM volume group had no free extents initially.
- Disk exhaustion caused database corruption risks and service crashes.

---

## 3. Root Cause

- Docker images, volumes, Home Assistant data, and Plex metadata filled the root filesystem.
- LVM logical volume was not expanded after increasing VM disk size.
- Insufficient memory exacerbated system instability.

---

## 4. Resolution Steps

### Step 1 — Resize VM Disk (Proxmox)
Increased VM disk size from 170 GB to 210 GB.

### Step 2 — Resize Partition
```
sudo growpart /dev/sda 3
```

### Step 3 — Resize Physical Volume
```
sudo pvresize /dev/sda3
```

### Step 4 — Extend Logical Volume
```
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```

### Step 5 — Resize Filesystem
```
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```

### Step 6 — Increase VM Memory
Upgraded RAM from 4 GB to 8 GB.

### Step 7 — Restart Services
Restarted PostgreSQL, Home Assistant, and Plex in dependency order.

### Step 8 — Cleanup (Optional)
```
docker system prune -a --volumes -f
journalctl --vacuum-size=500M
```

---

## 5. Outcome

- Root filesystem expanded to ~205 GB.
- Over 100 GB of free space available.
- All services running normally.
- Disk and memory related failures resolved.

---

## 6. Recommendations

- Move Docker and application data to NFS or dedicated storage.
- Monitor disk usage proactively.
- Always expand LVM after resizing VM disks.
- Maintain sufficient RAM headroom for Plex and HA workloads.
