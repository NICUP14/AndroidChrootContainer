# ACC - Android Chroot Container

`ACC` is a script that provides a lightweight and minimal chroot environment for rooted Android devices. It manages an isolated, native Linux-like userland without depending on Termux or additional user-space layers. It's ideal for power users, developers, and tinkerers who want to run a sandboxed Linux environment as close to the actual Android system.

## Features

- Small and portable.
- Runs a minimal Linux userland in a safe chroot environment.
- Mounts key Android system paths to provide near-native Linux functionality.
- Employs `unshare` to sandbox file system changes from the host Android system.
- Requires no `Termux` or user-space layers.

## Requirements

- A rooted Android device.
- A Linux filesystem tarball.
- An Android Terminal Emulator.
- A compatible busybox binary with:
  - ln
  - cp
  - chmod
  - mkdir
  - true
  - chroot
  - unshare
  - hostname
  - basename
  - mount
  - umount
  - mountpoint

## Disclaimer

```txt
The software is provided "as is", without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose, and noninfringement. In no event shall the authors or copyright holders be liable for any claim, damages, or other liability, whether in an action of contract, tort, or otherwise, arising from, out of, or in connection with the software or the use or other dealings in the software.
```

## Installation Guide

Recommended applications:

- [BusyBox](apk/BusyBox.apk)
- [Terminal Emulator](apk/TerminalEmulator.apk)
- [Hacker's Keyboard](apk/HackersKeyboard.apk)
- [Root Explorer](https://play.google.com/store/apps/details?id=com.speedsoftware.rootexplorer&hl=en-US)

The installation guide assumes only basic knowledge the Android system like installing files from unknown sources and minimal CLI experience.

### Busybox

For convenience, the project includes the free version of [Stericson's BusyBox Installer APK](apk/BusyBox.apk) and a pre-built standalone busybox binary for aarch64 ([Found Here!](bin/busybox)). Novice users are encouraged to use the graphical installer for simplicity. Both methods install the same version: `BusyBox v1.32.0-Stericson`. Alternatively, locate a compatible busybox binary that supports the above commands.

## Linux Filesystem Tarball

> [!NOTE]
> Ensure you are looking for pre-installed **ARM** (aarch64) linux rootfs packaged within a tarball archive (`*.tar.gz`), not live installers (`*.iso`), which are flashed onto installation medias like CDs and USBs.

Download your preferred Linux filesystem tarball to the system’s `Download` folder. For convienience, the projects includes a collection of compatible systems under [rootfs](rootfs). Next, open your terminal emulator and run the following commands:

```bash
# Acquire root permissions
su

# Remount /data for writing
mount -o remount,rw /data

# Create and populate the chroot folder
mkdir -p /data/linux/$ENV_NAME_HERE
tar -xfv /sdcard/Download/$TARBALL_NAME_HERE -C /data/linux/$ENV_NAME_HERE

# Remount back /data as read-only
mount -o remount,ro /data
```

## ACC

Use a file explorer that can access the root filesystem and can remount `/system` as read-write, such as Root Explorer. Copy the `acc` script into `/system/bin` or any location accessible through `$PATH`.  Alternatively keep it under `/sdcard/acc` and invoke it as:

```bash
su && sh /sdcard/acc /data/linux/$ENV_NAME_HERE
```

## Usage

```txt
acc: A lightweight utility to create safe, isolated chroot environments.
Usage: bin/acc <environment> <command> <command-args>
```

Inside the chroot shell, users can execute the `exit` command to leave the environment and automatically unmount the filesystems.

## Known Issues

- Arch Linux is currently unsupported due to unresolved `gnupg` issues.
- On some systems, non-root users may lack permission to run Linux binaries. If your system supports user-space isolation, you can use `unshare` with the `--user --map-root-user` options.