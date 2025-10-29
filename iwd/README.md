# iwd (Intel Wireless Daemon) - eduroam Configuration

## What is iwd?

**iwd** (Intel Wireless Daemon) is a modern, lightweight WiFi daemon for Linux. It's:
- ✅ Lightweight (minimal resource usage)
- ✅ Modern (supports latest WiFi standards)
- ✅ Simple to configure
- ✅ Part of iNet Wireless Daemon project

## Automatic Detection

If you have iwd installed, the eduroam installer will **automatically detect it** and configure accordingly:

```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py \
    --silent \
    --username user@institution.edu \
    --password pass
```

The script will:
1. ✅ Detect iwd is available
2. ✅ Generate eduroam config for iwd
3. ✅ Install to `/var/lib/iwd/eduroam.8021x` 
4. ✅ Set correct permissions (600, root:root)
5. ✅ Done! iwd daemon uses it automatically

No extra steps needed!

## Manual Configuration (Advanced)

If needed, manually create `/var/lib/iwd/eduroam.8021x`:

```ini
[Security]
EAP-Method=PEAP
EAP-Identity=anonymous@institution.edu
EAP-PEAP-CACert=embed:eduroam_ca_cert
EAP-PEAP-ServerDomainMask=server1.institution.edu:server2.institution.edu
EAP-PEAP-Phase2-Method=MSCHAPV2
EAP-PEAP-Phase2-Identity=your_username@institution.edu
EAP-PEAP-Phase2-Password=your_password

[Settings]
AutoConnect=true

[@pem@eduroam_ca_cert]
-----BEGIN CERTIFICATE-----
(your CA certificate here)
-----END CERTIFICATE-----
```

**Note:** Config file must have `600` permissions and be owned by `root:root`

```bash
sudo chmod 600 /var/lib/iwd/eduroam.8021x
sudo chown root:root /var/lib/iwd/eduroam.8021x
```

## Using iwd CLI (iwctl)

### List available networks
```bash
sudo iwctl
# In iwctl:
station wlan0 show
```

### Manually connect (if needed)
```bash
sudo iwctl
# In iwctl:
station wlan0 connect eduroam
```

### Disconnect
```bash
sudo iwctl
# In iwctl:
station wlan0 disconnect
```

## Troubleshooting iwd

### Connection Failed: "bad_certificate"

This means iwd's strict certificate validation failed. Your options:

**Option 1: Use wpa_supplicant (more lenient)**
```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --wpa_conf
```

**Option 2: Debug the certificate**
```bash
# Check iwd logs
journalctl -u iwd -f

# See what cert is being used
openssl x509 -in /path/to/ca.pem -text -noout
```

### iwd Daemon Not Starting

```bash
# Check logs
journalctl -u iwd -f

# Check if service is enabled
sudo systemctl enable iwd.service

# Start manually
sudo systemctl start iwd.service
```

### Config Not Found

Make sure:
1. Config is at `/var/lib/iwd/eduroam.8021x`
2. Permissions are `600` (owner read/write only)
3. Owner is `root:root`

```bash
sudo ls -la /var/lib/iwd/eduroam.8021x
```

### Certificate Issues

If certificate validation fails:

```bash
# Verify CA cert format
file /var/lib/iwd/eduroam.8021x

# Check certificate validity
openssl x509 -in embedded_cert.pem -noout -dates

# Try debug mode
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --debug
```

## iwd vs wpa_supplicant

| Feature | iwd | wpa_supplicant |
|---------|-----|-----------------|
| Resource usage | Very low | Low |
| Certificate validation | Strict | Lenient |
| PEAP support | ✅ Yes | ✅ Yes |
| TTLS support | ✅ Yes | ✅ Yes |
| PWD support | ✅ Yes | ✅ Yes |
| Modern | ✅ Yes | Mature |
| Works on all systems | No | ✅ Yes |

**Use iwd if:** You want modern, lightweight daemon on supported systems  
**Use wpa_supplicant if:** Compatibility is more important than minimal resource usage

## Switching Between Backends

### From iwd to wpa_supplicant

```bash
# Stop iwd
sudo systemctl stop iwd.service
sudo systemctl disable iwd.service

# Generate wpa_supplicant config
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --wpa_conf

# Use wpa_supplicant
sudo wpa_supplicant -i wlan0 -c ~/.config/cat_installer/cat_installer.conf
```

### From wpa_supplicant to iwd

```bash
# Install iwd
sudo pacman -S iwd

# Install eduroam config for iwd
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --install-iwd

# Enable iwd
sudo systemctl enable iwd.service
sudo systemctl start iwd.service
```

## Permissions and Security

### iwd Config Permissions

The installer automatically sets correct permissions:
```bash
-rw------- 1 root root 6437 eduroam.8021x
```

**Why 600?** Only root can read/write (contains password)

### Verify Permissions

```bash
sudo stat /var/lib/iwd/eduroam.8021x
# Access: (0600/-rw-------)  Uid: (   0/ root)   Gid: (   0/ root)
```

## References

- [iwd Project](https://iwd.wiki.kernel.org/)
- [iwd Man Pages](https://man.archlinux.org/man/iwd.1)
- [systemd-networkd with iwd](https://wiki.archlinux.org/title/Systemd-networkd#iwd)

---

**iwd is fully supported!** Use it for modern, lightweight eduroam setup ✅
