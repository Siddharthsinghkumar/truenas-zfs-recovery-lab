
# 🛠️ TrueNAS ZFS Recovery Lab – Simulated Failure and Recovery

This homelab experiment demonstrates my ability to configure, break, and recover a ZFS RAID-Z array using mixed disks on TrueNAS SCALE. It involved simulating disk failure during runtime and using both CLI and forensic recovery tools to restore the data pool.

---

## ⚙️ System Overview

| Component   | Type             | Details                       |
|------------|------------------|-------------------------------|
| Disk 1     | HDD (SMR)        | 230 GB                        |
| Disk 2     | HDD (CMR)        | 500 GB                        |
| Disk 3     | HDD (CMR)        | 1 TB                          |
| Disk 4     | SSD              | 500 GB                        |
| Boot Drive | USB 2.0 Pendrive | TrueNAS SCALE boot            |

- **ZFS RAID-Z1** across all 4 data drives (auto-sized to 230 GB per disk → ~690 GB usable).
- Data: ~50 GB test files (MKV, MP4 movie files) copied from another system.
- **TrueNAS SCALE** OS was booted from USB pendrive.

---

## 💣 Simulated Disk Failure

While TrueNAS was powered on:
- Unplugged **3 out of 4 drives**, including the SSD.
- Also removed the **boot pendrive**.
- No write operation was active during failure (static file storage only).

> 🧠 Outcome: The ZFS pool was corrupted and could not auto-import. System showed critical fault in Web UI.

---

## 🧪 CLI Recovery Attempts (Failed)

Attempted via terminal after reconnecting drives:
- `zpool status`
- `zpool clear <pool>`
- `zpool scrub <pool>`
- `zdb -ul /dev/sdX`
- `zpool import -F -T <txg>`

Result: **No success**. Pool still marked as FAULTED or UNAVAILABLE.

---

## 🧰 Final Recovery Using Klennet ZFS Recovery (Windows)

Used **Klennet ZFS Recovery** tool on a Windows 10 machine:

- Auto-detected pool layout (RAID-Z1)
- Handled mixed SMR/CMR disks with different capacities
- Verified and listed all recoverable files
- Recovered all MKV/MP4 test files
- Verified checksums were intact

> 🎯 Successfully recovered test dataset despite serious vdev and disk mismatch issues.

---

## 🧼 Final Pool Rebuild

After data recovery:
- Destroyed damaged vdevs
- Created new **500 GB mirror pool** using the 500 GB + 1 TB HDD
- SSD configured as fast single-drive pool (no redundancy)
- 230 GB SMR used as hot-spare / swap
- Pendrive + 230 GB drive used for dual-boot boot pool (TrueNAS)

---

## 🧵 Lessons Learned

- Mixed disk types (SMR + CMR) cause issues under RAID-Z failure conditions
- TrueNAS SCALE ZFS tools are limited under extreme failures
- Manual import commands work only with intact txg records
- External recovery tools like Klennet are essential in deep-failure scenarios

---


## 💡 Built By

**Siddharth Singh**  
