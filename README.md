# smeg — Setup Mobile Explotation Groundwork

A bash script to automate the setup of an Android mobile pentest environment. Given a proxy certificate, smeg handles emulator creation, certificate installation into the system trust store, and Frida server deployment — all in one command.

---

## What it does

1. Optionally creates and/or launches an Android emulator (AVD)
2. Waits for ADB to connect and roots the device
3. Installs a proxy certificate (Burp, mitmproxy, etc.) directly into the system trust store
4. Auto-detects device architecture and downloads + installs the latest Frida server
5. Optionally installs a target APK
6. Reboots the device to apply changes

---

## Requirements

- Android SDK (emulator, platform-tools, cmdline-tools)
- `adb`
- `openssl`
- `curl` + `xz`
- `avdmanager` (only needed for `-n`)
- Frida (`pip install frida-tools`)

The script expects the Android SDK at `~/Android/Sdk`. If yours is elsewhere, update the `ANDROID_SDK_ROOT` line at the top of the script.

### Install Android SDK (if needed)

Download Android Studio or the standalone command-line tools from the Android developer site, then install the required system image:

```bash
sdkmanager "platform-tools" "emulator"
sdkmanager "system-images;android-37.0;google_apis_ps16k;x86_64"
```

---

## Setup

```bash
git clone https://github.com/joshgreen95/smeg
cd smeg
chmod +x smeg
```

No dependencies to install beyond the Android SDK and standard tools listed above.

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

The AVD settings are defined at the top of the script:

```bash
AVD_NAME="Pixel_9"
AVD_PACKAGE="system-images;android-37.0;google_apis_ps16k;x86_64"
AVD_DEVICE="pentest_avd"
```

Change these to match your preferred system image or device profile before running.

---

## After setup

Once smeg completes and the device reboots:

- **Verify cert:** Settings -> Security -> Trusted Credentials -> System
- **Start Frida:** `adb root && adb shell /data/local/tmp/frida-server &`
- **Check processes:** `frida-ps -U`

---

## Notes

- Designed for use with Android emulators launched with `-writable-system`. Physical devices must already be rooted.
- The script uses `adb remount` to make the system partition writable, which requires a rooted/writable-system emulator.
- Frida server is fetched fresh from the latest GitHub release on each run to avoid version mismatches with your local `frida-tools`.

