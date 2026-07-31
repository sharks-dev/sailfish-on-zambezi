# Installing on Zambezi

Right, I have provided three files:

 - `SailfishOScommunity-release-5.1.0.11-xqdc72-debug1.zip`
 - `hybris-boot-zambezi.img`
 - `debug_zambezi.zip`

Note - you may have to unzip `SailfishOScommunity-release-5.1.0.11-xqdc72-debug1.zip` and adjust `META-INF/com/google/android/updater-script` to suit your device if you do not have an `XQ-DC72` but instead own an `XQ-DC54`. Re-zip everything up when you've made your changes and use your modified zip for the remainder of these instructions. The same change may be required in `debug_zambezi.zip`.

Steps are as follows - note, instructions written from memory, forgive me if there are minor mistakes.

1. Ensure you are running Lineage in slot a (refer to [murray instructions](https://github.com/sharks-dev/sailfish-on-murray/blob/main/INSTALLING.md) if you require help)

2. Reboot to bootloader (note: not fastbootd. You should have a blank screen with a blue LED, not a "LineageOS" branded screen). Run `fastboot erase userdata && fastboot format:ext4 userdata`. Note you cannot use the fastboot 34.0.5-debian for this, you must download the latest fastboot 37.0.0-14910828 at the time of writing.

3. Run `fastboot reboot recovery`, then in the recovery screen head to "Apply Update" and "Apply Update from ADB". Apply the update with `adb -d sideload SailfishOScommunity-release-5.1.0.11-xqdc72-debug1.zip`. There will be warnings in the recovery screen saying that the image verification failed or something like that. You can ignore them and continue anyway.

4. In the recovery screen, head to Advanced --> Enable ADB.

5. Run `adb shell` followed by `vi /etc/systemd/journald.conf`. Press "i", then change `volatile` to `automatic`. Press "esc", then ":wq" and enter. `exit` the adb shell.

6. Run `adb reboot bootloader`, then `fastboot flash boot hybris-boot-zambezi.img`.

7. Run `fastboot reboot`. 

At this stage many things could happen.

 - Best case scenario, SailfishOS boots after a few minutes of looking at the SONY logo. You are able to interact with the screen and set up the phone, confirm ofono, wifi, etc. all working. Use the CSD tool (head to Settings app, about product, tap Build Number 5 times).
 - Next best case, SailfishOS boots but you cannot interact with the phone or have limited functionality. If the touchscreen does not work and you have access to a USB mouse you can connect to the USB-C port, try that. Run the CSD tool as above and confirm what else does or doesn't work. If WiFi works, you should be able to SSH into the phone.
 - Next best case, the SailfishOS rootfs mounts but you don't get GUI. This will be detected by the _lack_ of a network interface visible on your computer - you should see a network interface come up during the initramfs, then go away after sailfish boots. Check after a few minutes looking at the SONY logo with `ip a` to determine if a network interface exists. (obviously the phone should still be connected to the computer over USB).
 - Worst case, the phone failed ot boot. In this case, the network will still be up - but you likely won't have an IP address. Run `ip a` to determine what the interface is (eg. `enx123456789`) and run `sudo ip addr add 192.168.2.20/24 dev enx123456789`. You can now `telnet 192.168.2.15 2323` or `telnet 192.168.2.15 23` depending on how far through the boot process we got. 

At any rate, what I will need from you is the logs. If you managed to run the CSD tool, that's fantastic - screenshot the results from there (just the main page with the red and green boxes) and send that back to me please.

Additionally, if you can get the rootfs booted and login via telnet (or SSH over WiFi), reboot and after about two minutes save the output of `/usr/libexec/droid-hybris/system/bin/logcat` for me please.

Regardless of whether you managed to get to the CSD tool or run logcat, I need journal logs - especially if the phone failed to boot at all. These are saved at `/var/log/journal`. If you cannot get into the system via GUI or SSH or telnet, extract them from the phone by rebooting to bootloader, running `fastboot flash boot /path/to/your/lineageOS/boot.img`, `fastboot reboot recovery`, then sideloading `adb -d sideload zambezi_debug.zip`, enabling ADB and running `adb pull /data/.stowaways/sailfishos/var/log/journal/ /path/to/where/it/should/be/saved/`. You can then either wipe userdata to restore LineageOS or reflash `hybris-boot-zambezi.img` to return to playing in Sailfish.
