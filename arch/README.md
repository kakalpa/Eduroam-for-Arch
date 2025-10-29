# Arch Linux - eduroam Setup Guide

## Quick Installation for Arch Linux

The script **auto-detects your network management tool** and configures accordingly!

### 1. Run the Installer

```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py \
    --silent \
    --username your_username@turkuamk.fi \
    --password your_password
```

### 2. That's it! ✅

The script will automatically:
- Detect NetworkManager, iwd, or wpa_supplicant (whichever is installed)
- Generate appropriate config
- Install with correct permissions
- You're connected to eduroam!

## What Gets Installed?

Depending on what's available on your system:

| System Setup | Detected | Config Location |
|---|---|---|
| GNOME/KDE with NetworkManager | ✅ NM | DBus system bus |
| systemd + iwd | ✅ iwd | `/var/lib/iwd/eduroam.8021x` |
| Minimal/server + wpa_supplicant | ✅ wpa | `~/.config/cat_installer/` |

No manual setup needed - the script handles it all!

## See What Backend Was Chosen

Run with `--debug` to see which backend was auto-detected:

```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py \
    --silent \
    --username user@turkuamk.fi \
    --password pass \
    --debug

# Look for: DEBUG:Backend selection: ...
```

## Verification

### Verify Installation

Check if config was created for your backend:

```bash
# For NetworkManager
nmcli con show eduroam

# For iwd
sudo cat /var/lib/iwd/eduroam.8021x | head -5

# For wpa_supplicant
cat ~/.config/cat_installer/cat_installer.conf | head -5
```

## Troubleshooting

### "iwd connection fails (bad_certificate)"

iwd has stricter certificate validation. The script handles this by using wpa_supplicant which is more compatible.

If you need to force wpa_supplicant:

```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --wpa_conf
```

Then manually connect:
```bash
sudo wpa_supplicant -B -i wlan0 -c ~/.config/cat_installer/cat_installer.conf
```

### Which backend was selected?

Check the debug output:

```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --debug
```

Look for: `DEBUG:Backend selection: ...`

## Support

For Arch-specific issues:
- [Arch Wiki - iwd](https://wiki.archlinux.org/title/Iwd)
- [Arch Wiki - NetworkManager](https://wiki.archlinux.org/title/NetworkManager)
- [Arch Wiki - wpa_supplicant](https://wiki.archlinux.org/title/Wpa_supplicant)

---

**Arch Linux is fully supported!** ✅
