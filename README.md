# Helper for signing DKMS modules for use with Secure Boot

Running a system with Secure Boot enable has its advantages and disadvantages. One of the disadvantages are that DKMS modules require a bit of special handing. Because they're compiled on your own system, not the Debian maintainer's (or whoever signs the official modules), they are not signed with an officially recognized key. With Secure Boot enabled, the kernel will refuse to load them.

One of the solutions is to create a MOK signing key yourself, enroll it on your machine, and sign all your modules with it. Doing so will effectively whitelist your modules, and the kernel will happily accept them.

Rather than manually iterating over all the modules and running the sign-file script on them, wouldn't it be nice with a helper script? That's exactly what this script is. You provide a MOK keypair and the script does all the signing.

See the [Debian wiki](https://wiki.debian.org/SecureBoot) for more information on how Secure Boot works. Here's how they suggest you create and enroll your key:

```bash
# openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out MOK.der -days 36500 -subj "/CN=My Name/" -nodes

# mokutil --import MOK.der // prompts for one-time password

# mokutil --list-new // recheck your key will be prompted on next boot

<rebooting machine then enters MOK manager EFI utility: enroll MOK, continue, confirm, enter password, reboot>

# dmesg | grep cert // verify your key is loaded
```
