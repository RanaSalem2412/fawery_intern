## Task Q2 Solution (in English)

---

# 🔢 Full Troubleshooting and Fix

## 1. Verify DNS Resolution

### - Using default `/etc/resolv.conf` DNS:

```bash
dig internal.example.com
```

**Result:** NXDOMAIN (domain not found)

---

### - Using Google Public DNS (8.8.8.8):

```bash
dig internal.example.com @8.8.8.8
```

**Result:** NXDOMAIN (domain not found)

---

## 2. Diagnose Service Reachability

### - Testing service access:

```bash
curl http://internal.example.com
```

**Result:** Apache test page appeared ✅

---

### - Checking if port 80 is open locally:

```bash
ss -tuln | grep :80
```

or

```bash
netstat -tuln | grep :80
```

**Result:** Apache is listening on port 80.

---

## 3. Possible Causes for "host not found" Error

| Cause | Explanation |
|:------|:------------|
| 1. Missing DNS record | Internal DNS server does not know about internal.example.com |
| 2. Wrong `/etc/resolv.conf` settings | Device is using wrong DNS server |
| 3. Corrupted DNS cache | Local device caching wrong data |
| 4. Network or routing issue | DNS server unreachable due to network problems |
| 5. Missing `/etc/hosts` entry | Local machine has no manual override |

---

## 4. Proposed Fixes and Commands

| Issue | How to Confirm | Command to Fix |
|:------|:--------------|:---------------|
| Missing DNS Record | `dig internal.example.com @<DNS-IP>` | Add record on DNS server or manually in `/etc/hosts` |
| Wrong `/etc/resolv.conf` | `cat /etc/resolv.conf` | Update the file to point to correct DNS |
| Corrupted DNS Cache | `systemd-resolve --statistics` | Flush cache: `sudo systemd-resolve --flush-caches` |
| Network Routing Issue | `ping <DNS-IP>` / `traceroute <DNS-IP>` | Fix routing or network configs |
| No `/etc/hosts` entry | `cat /etc/hosts` | Add manual mapping in `/etc/hosts` |

---

### Example: Editing `/etc/hosts`

```bash
sudo nano /etc/hosts
```

Add the line:

```
172.31.17.96 internal.example.com
```

Then test again:

```bash
curl http://internal.example.com
```

---



