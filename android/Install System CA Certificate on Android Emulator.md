## 3. Insert certificate into system certificate store

Now we have to place our CA certificate inside the system certificate store located at `/system/etc/security/cacerts/` in the Android filesystem. By default, the `/system` partition is mounted as read-only. The following steps describe how to gain write permissions on the `/system` partition and how to copy the certificate created in the previous step.

### [#](https://docs.mitmproxy.org/stable/howto-install-system-trusted-ca-android/#instructions-for-api-level--28)  Instructions for API LEVEL > 28

Starting from API LEVEL 29 (Android 10), it seems to be impossible to mount the “/” partition as read-write. Google provided a [workaround for this issue](https://android.googlesource.com/platform/system/core/+/master/fs_mgr/README.overlayfs.md) using OverlayFS. Unfortunately, at the time of writing this (11. April 2021), the instructions in this workaround will result in your emulator getting stuck in a [boot loop](https://issuetracker.google.com/issues/144891973). Some smart guy on Stackoverflow [found a way](https://stackoverflow.com/questions/60867956/android-emulator-sdk-10-api-29-wont-start-after-remount-and-reboot) to get the `/system` directory writable anyway.

**Keep in mind:** You always have to start the emulator using the `-writable-system` option if you want to use your certificate. Otherwise Android will load a “clean” system image.

Tested on emulators running API LEVEL 29 and 30

#### [#](https://docs.mitmproxy.org/stable/howto-install-system-trusted-ca-android/#instructions-1)  Instructions

-   List your AVDs: `emulator -list-avds` (If this yields an empty list, create a new AVD in the Android Studio AVD Manager)
-   Start the desired AVD: `emulator -avd <avd_name_here> -writable-system` (add `-show-kernel` flag for kernel logs)
-   restart adb as root: `adb root`
-   disable secure boot verification: `adb shell avbctl disable-verification`
-   reboot device: `adb reboot`
-   restart adb as root: `adb root`
-   perform remount of partitions as read-write: `adb remount`. (If adb tells you that you need to reboot, reboot again `adb reboot` and run `adb remount` again.)
-   push your renamed certificate from step 2: `adb push <path_to_certificate> /system/etc/security/cacerts`
-   set certificate permissions: `adb shell chmod 664 /system/etc/security/cacerts/<name_of_pushed_certificate>`
-   reboot device: `adb reboot`

### [#](https://docs.mitmproxy.org/stable/howto-install-system-trusted-ca-android/#instructions-for-api-level--28-1)  Instructions for API LEVEL <= 28

Tested on emulators running API LEVEL 26, 27 and 28

**Keep in mind:** You always have to start the emulator using the `-writable-system` option if you want to use your certificate. Otherwise Android will load a “clean” system image.

-   List your AVDs: `emulator -list-avds` (If this yields an empty list, create a new AVD in the Android Studio AVD Manager)
-   Start the desired AVD: `emulator -avd <avd_name_here> -writable-system` (add `-show-kernel` flag for kernel logs)
-   restart adb as root: `adb root`
-   perform remount of partitions as read-write: `adb remount`. (If adb tells you that you need to reboot, reboot again `adb reboot` and run `adb remount` again.)
-   push your renamed certificate from step 2: `adb push <path_to_certificate> /system/etc/security/cacerts`
-   set certificate permissions: `adb shell chmod 664 /system/etc/security/cacerts/<name_of_pushed_certificate>`
-   reboot device: `adb reboot`