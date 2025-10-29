# eduroam Linux Installer - iwd/Arch Edition

A single-file CAT (Configuration Assistant Tool) Linux installer for eduroam WiFi configuration with **fully automatic backend detection**.

For Turku University of Applied Sciences students 

## Features

✅ **Automatic Backend Detection**
- NetworkManager (GNOME/KDE desktops)
- iwd (Intel Wireless Daemon) 
- wpa_supplicant (universal fallback)

✅ **Zero Configuration Required**
- Just run the script
- No flags or decisions needed
- Works on any Linux system

✅ **Multi-EAP Support**
- PEAP with MSCHAPV2
- TTLS with PAP
- EAP-PWD

✅ **Production Ready**
- Tested on multiple systems
- Full error handling
- Comprehensive documentation

## Quick Start

### Automatic Installation (Recommended)
```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py \
    --silent \
    --username user@institution.edu \
    --password yourpassword
```

The script will:
1. Auto-detect your system's network manager
2. Gather required credentials
3. Install eduroam configuration
4. Done! ✅

### Interactive Mode
```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py
```

### For Arch Linux Specifically
See [arch/README.md](arch/README.md) for Arch-specific installation and configuration.

### For iwd Users
See [iwd/README.md](iwd/README.md) for iwd-specific setup and troubleshooting.

## Documentation

- **[docs/README.md](docs/README.md)** - Main user guide
- **[docs/QUICK_REFERENCE.md](docs/QUICK_REFERENCE.md)** - Quick start guide
- **[docs/AUTOMATED_SELECTION_GUIDE.md](docs/AUTOMATED_SELECTION_GUIDE.md)** - How automation works
- **[docs/BACKEND_LOGIC_EXPLAINED.md](docs/BACKEND_LOGIC_EXPLAINED.md)** - Technical details
- **[docs/INSTALLATION_SUCCESS.md](docs/INSTALLATION_SUCCESS.md)** - Testing & verification
- **[arch/README.md](arch/README.md)** - Arch Linux specific guide
- **[iwd/README.md](iwd/README.md)** - iwd daemon setup guide

## Files Structure

```
eduroam/
├── installer/
│   └── eduroam-linux-TUoAS-eduroam_students.py    [Main script]
├── docs/
│   ├── README.md
│   ├── QUICK_REFERENCE.md
│   ├── AUTOMATED_SELECTION_GUIDE.md
│   ├── BACKEND_LOGIC_EXPLAINED.md
│   └── INSTALLATION_SUCCESS.md
├── arch/
│   ├── README.md                                   [Arch Linux guide]
│   └── PKGBUILD                                    [Arch package build]
├── iwd/
│   ├── README.md                                   [iwd setup guide]
│   ├── 8021x-example.conf                          [Example config]
│   └── troubleshoot.md                             [Troubleshooting]
├── tests/
│   └── test_backends.py                            [Backend tests]
└── README.md                                        [This file]
```

## Backend Selection Priority

The script automatically chooses the best backend:

```
1. NetworkManager (if DBus available)
2. iwd (if /var/lib/iwd exists)
3. wpa_supplicant (always available)
```

## System Requirements

- Linux kernel 5.0+
- Python 3.6+
- One of: NetworkManager, iwd, or wpa_supplicant

### Arch Linux Specific
```bash
pacman -S python3 networkmanager iwd wpa_supplicant
```

## Installation Locations

| Backend | Location |
|---------|----------|
| NetworkManager | DBus system bus |
| iwd | `/var/lib/iwd/eduroam.8021x` |
| wpa_supplicant | `~/.config/cat_installer/cat_installer.conf` |

## Usage Examples

### Basic Silent Installation
```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py \
    --silent \
    --username user@institution.edu \
    --password pass123
```

### Debug Mode (See Which Backend Was Chosen)
```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py \
    --silent \
    --username user@institution.edu \
    --password pass123 \
    --debug
```

### Force Specific Backend (Power Users)
```bash
# Force wpa_supplicant
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --wpa_conf

# Generate iwd config locally
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --iwd_conf

# Install iwd system-wide
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --install-iwd
```

## Troubleshooting

### "Connection fails with bad_certificate"
This usually means iwd's strict certificate validation failed. Try wpa_supplicant:
```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --wpa_conf
```

### "Which backend was selected?"
Run with `--debug`:
```bash
python3 installer/eduroam-linux-TUoAS-eduroam_students.py --debug
```
Look for: `DEBUG:Backend selection: ...`

### "How do I verify installation?"
```bash
# NetworkManager
nmcli con show eduroam

# iwd
sudo cat /var/lib/iwd/eduroam.8021x | head -5

# wpa_supplicant
cat ~/.config/cat_installer/cat_installer.conf | head -5
```

## For System Administrators

### Deploy to Multiple Systems
```bash
#!/bin/bash
for host in $(cat hosts.txt); do
    ssh "$host" 'python3 eduroam-linux-TUoAS-eduroam_students.py \
        --silent \
        --username $USER@institution.edu \
        --password $TEMP_PASS'
done
```

Each system auto-detects its backend automatically. ✅

## Arch Linux AUR Package

This project is available as an Arch Linux package. See [arch/README.md](arch/README.md) for AUR installation.

## Contributing

Contributions are welcome! For major changes, please open an issue first to discuss proposed changes.

## Testing

Run the test suite:
```bash
python3 -m pytest tests/
```

## License

See [COPYING](COPYING) for license information.

## Authors

- Original: Tomasz Wolniewicz, Michał Gasewicz
- iwd/Automation Enhancement: Oct 2025
- Contributors: See top of installer script

## Support

For issues, please check:
1. [docs/README.md](docs/README.md)
2. [docs/QUICK_REFERENCE.md](docs/QUICK_REFERENCE.md)
3. Run with `--debug` flag
4. See relevant guide (arch/iwd) for specific issues

---

**Status:** ✅ Production Ready  
**Latest Update:** October 29, 2025  
**Tested Backends:** All (NM, iwd, wpa_supplicant)
