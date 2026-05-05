# smeg — Setup Mobile Explotation Groundwork

A bash script to automate the setup of an Android mobile pentest environment.

## What it does

1. Optionally creates and/or launches an Android emulator (AVD)
2. Roots the device
3. Installs a proxy certificate (Burp, mitmproxy, etc.) directly into the system trust store
4. Auto-detects device architecture and downloads + installs the latest Frida server
5. Optionally installs a target APK

---
## Requirements

- Android SDK (emulator, platform-tools, cmdline-tools)
- `adb`
- `avdmanager` 
- Frida (`pip install frida-tools`)

The script expects the Android SDK at `~/Android/Sdk`. If yours is elsewhere, update the `ANDROID_SDK_ROOT` line at the top of the script.

---

## Usage

```
./smeg -c <cert.der|cert.pem> [-a <target.apk>] [-e] [-n]

  -c    Proxy certificate file in DER or PEM format [required]
  -a    APK to install on the device after setup
  -e    Launch the emulator before setup
  -n    Create the AVD then launch it (implies -e)
```

### Examples

**First time — create AVD, launch emulator, full setup:**
```bash
./smeg -n -c burp.der
```

**Existing AVD — launch emulator + setup:**
```bash
./smeg -e -c burp.der
```

**Physical device or already-running emulator:**
```bash
./smeg -c burp.der
```

**With a target APK:**
```bash
./smeg -e -c burp.der -a target.apk
```

---

## Configuration

The AVD settings are defined at the top of the script
