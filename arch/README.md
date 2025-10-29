# Arch Linux - eduroam Setup Guide

## Quick Installation for Arch Linux

### 1. Install Dependencies

```bash
sudo pacman -S python3 networkmanager iwd wpa_supplicant
```

### 2. Run the Installer

```bash
# Interactive mode (recommended for first-time users)
python3 installer/eduroam-linux-TUoAS-eduroam_students.py

# Silent mode
python3 installer/eduroam-linux-TUoAS-eduroam_students.py \
    --silent \
    --username your_username@turkuamk.fi \
    --password your_password
```

### 3. That's it! ✅

The script auto-detects which backend to use:
- **NetworkManager** (if you have GNOME/KDE with NM)
- **iwd** (if you have iwd installed)
- **wpa_supplicant** (fallback - always available)

## Arch-Specific Configuration

### For Systemd Users

If using systemd-networkd with iwd:

```bash
# Enable iwd
sudo systemctl enable iwd.service
sudo systemctl start iwd.service

# Run installer
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --install-iwd
```

### For NetworkManager Users

```bash
# Enable NetworkManager
sudo systemctl enable NetworkManager.service
sudo systemctl start NetworkManager.service

# Run installer (will auto-detect NM)
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --silent \
    --username user@turkuamk.fi \
    --password pass
```

### For wpa_supplicant Users

```bash
# Generate config
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --wpa_conf

# Connect (manual)
sudo wpa_supplicant -B -i wlan0 -c ~/.config/cat_installer/cat_installer.conf
dhclient wlan0
```

## Arch-Specific Notes

### Minimal Installation (no NM, no iwd)
If you only have wpa_supplicant:

```bash
sudo pacman -S wpa_supplicant
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --wpa_conf
```

### iwd Recommended for Arch
iwd is lightweight and modern - recommended for Arch users:

```bash
sudo pacman -S iwd
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --install-iwd
```

## Troubleshooting on Arch

### "Module not found: dbus"
```bash
sudo pacman -S dbus python-dbus
```

### "iwd connection fails (bad_certificate)"
Switch to wpa_supplicant:
```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --wpa_conf
```

### "Permission denied for /var/lib/iwd"
Use pkexec (usually works) or run as root:
```bash
sudo python3 installer/eduroam-linux-TUoAS-eduroam_students.py --install-iwd
```

## Testing Connection

### For NetworkManager
```bash
nmcli con show eduroam
nmcli con up eduroam
```

### For iwd
```bash
sudo iwctl
# In iwctl prompt:
station wlan0 show
```

### For wpa_supplicant
```bash
sudo wpa_supplicant -i wlan0 -c ~/.config/cat_installer/cat_installer.conf -v
```

## AUR Package (Future)

Coming soon! eduroam-linux-installer

## Support

For Arch-specific issues, check:
- [Arch Wiki - iwd](https://wiki.archlinux.org/title/Iwd)
- [Arch Wiki - NetworkManager](https://wiki.archlinux.org/title/NetworkManager)
- [Arch Wiki - wpa_supplicant](https://wiki.archlinux.org/title/Wpa_supplicant)

Or run with `--debug` flag to see what was auto-selected:

```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --debug
```

---

**Arch Linux is fully supported!** ✅
