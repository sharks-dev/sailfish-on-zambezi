# irclogs

[As on murray](https://github.com/sharks-dev/sailfish-on-murray/blob/main/IRCLOGS.md), I have copied the relevant IRCLogs of my conversations relating to this port on #sailfishos-porters and pasted them here. This time around, they are mostly just me forgetting how I did things the first time around. But they might still come in handy if I ever need to retrace my steps.

---
### 📅 2026-07-27

| Time | User & Message |
| :--- | :--- |
| 07:02:04 | *&lt;sharks_&gt;*: @rinigus, How difficult is it to add another device (on the same kernel) to my existing project, similar to how Nagara has pdx223 and pdx224? If I added the hardware repositories to my local_manifests and new spec files to droid-hal-version-device, droid-hal-device and droid-config-device, what else would I need to do? Do I need two individual `~/.hadk.env` files? When I run `make hybris-hal droidmedia`, how do I now which device it spits |
| 07:02:10 | *&lt;sharks_&gt;*: I ran `breakfast` for? |
| 07:02:32 | *&lt;sharks_&gt;*: Is this a journey I want to undertake or is it more work than I expect/hope? |
| 07:12:10 | *&lt;sharks_&gt;*: Oh and a new set of patterns. What else am I forgetting..? |
| 08:35:49 | *&lt;sharks_&gt;*: Hmm, reading up on it some more it seems like you need a separate $ANDROID_ROOT for every device. Bugger, I'll have to find the disk space and download the sources all again. |
| 10:18:11 | **&lt;elros34&gt;**: very unlikely you need to download all sources if device is almost the same |
| 10:20:26 | *&lt;sharks_&gt;*: So my understanding was that if I ran eg. `make hybris-hal`, it would not know which device tree to grab stuff from and would just grab everything it could find? |
| 10:21:09 | *&lt;sharks_&gt;*: Anyway, on further digging the source tree is not even that big, it's the .repo folder which is huge. |
| 12:38:49 | &lt;rinigus&gt;: sharks: you don't need multiple sources, just slightly different .hadk.env. on compilation, different devices will be in different out subfolders |
| 12:39:11 | &lt;rinigus&gt;: overall, relatively easy. look also into tama main project and its docs and scripts |

---
### 📅 2026-07-28

| Time | User & Message |
| :--- | :--- |
| 03:41:06 | *&lt;sharks__&gt;*: Thanks rinigus. When I get a little bit further along and have proved to myself that what I'm attempting is possible, I'll do that. |
| 03:47:00 | *&lt;sharks__&gt;*: In the meantime, I have a new problem, can someone help me please? I have "%define makefstab_skip_entries /system /system_root /odm /vendor ...etc" in rpm/droid-hal-$device.spec, but when trying to make libhybris with `build–packages.sh --mw`, I am told that system.mount and system_root.mount are conflicting files between droid-hal-$habuild_device.rpm and droid-config-$device.rpm. All the other mounts defined in "ma |
| 03:47:41 | *&lt;sharks__&gt;*: I'm going mad trying to work out why it won't respect the request to skip those two mounts. What have I missed?? |
| 04:00:31 | *&lt;sharks__&gt;*: Sorry, message got cut off... All the other mounts defined in "makefstab_skip_entries" are skipped, and I've confirmed this by removing them and observing that the libhybris log now complains about them also, but I can't get it to skip system_root and system. |
| 04:47:10 | **&lt;elros34&gt;**: why do you even build libhybris? You build it once and forget. Build droid-config and show log file |
| 04:47:24 | **&lt;elros34&gt;**: droid-hal* not droid-config |
| 04:53:34 | *&lt;sharks__&gt;*: This is the once, I'm effectively starting a new port here. |
| 04:57:20 | **&lt;elros34&gt;**: are you sure you have system_root in fstab to skip it? |
| 04:58:20 | *&lt;sharks__&gt;*: paste.opensuse.org/pastes/2ae4d020ce39 |
| 05:07:28 | *&lt;sharks__&gt;*: And no, system_root is not in any fstab. So how does it end up in droid-hal? And why does /system not get skipped anyway? |
| 05:09:07 | **&lt;elros34&gt;**: / in fstab generates /system and /system_root |
| 05:11:19 | *&lt;sharks__&gt;*: Are you saying I should have / in %define makefstab_skip_entries? I did not have that in my 10IV port! |
| 05:12:48 | **&lt;elros34&gt;**: I do not know what you have in fstab so I don't know. / and /system in makefstab_skip_entries are my  guess |
| 05:19:38 | *&lt;sharks__&gt;*: I'm not sure which fstab file I should be looking at, or why they'd be significantly different between these two ports. That said, thanks again for your help elros, as it appears adding "/" to makefstab_skip_entries has worked. Yet to see if it has any undue side effects, but I'll roll with it for now. |
| 05:20:02 | *&lt;sharks__&gt;*: However, I still fail to build libhybris as I am missing android-config.h, despite it being in droid-hal-$device-devel.rpm/usr/include/droid-devel/droid-headers/android-config.h |
| 05:20:59 | **&lt;elros34&gt;**: are you sure you are not mixing devices and targets? |
| 05:23:07 | *&lt;sharks__&gt;*: I replaced ~/.hadk.env and set up a completely new $ANDROID_ROOT and $PLATFORM_SDK_ROOT to try and avoid that |
| 05:23:54 | *&lt;sharks__&gt;*: I planned one day to bring them together once I trusted what I was doing, but for now I was keeping them apart |
| 05:24:39 | *&lt;sharks__&gt;*: libhybris log --> paste.opensuse.org/pastes/c8b7f334793a |
| 05:30:08 | **&lt;elros34&gt;**: maybe check in target whether you have correct droid-hal installed: sb2 -t $VENDOR-$DEVICE-$PORT_ARCH -m sdk-install -R and then zypper se -s droid-hal |
| 05:33:28 | *&lt;sharks__&gt;*: Output from that is clean as a whistle. All the correct device. |

---
### 📅 2026-07-30

| Time | User & Message |
| :--- | :--- |
| 10:42:23 | *&lt;sharks_&gt;*: Worked out my problem. Elros was close, it was not the target/tooling that was wrong but my droid-config-$device.spec had a typo in it and was looking at the patterns files for the old device, which were still (I thought) harmlessly present in my new source tree. After deleting them it threw an error that it couldn't find them, which alerted me to the problem because why was it looking for them?? |
| 10:44:49 | *&lt;sharks_&gt;*: Oh, nevermind. Still got "checking for android-config.h... no --- configure: error: required header file is missing". I celebrated too soon |
| 11:24:08 | *&lt;sharks_&gt;*: Okay! Finally got libhybris to built! |
| 11:25:17 | *&lt;sharks_&gt;*: If you are curious what killed it, see https://paste.opensuse.org/pastes/c0cc787cde31 - I don't know why, but I was missing "#ifndef DISABLED_FOR_HYBRIS_SUPPORT" clauses in $ANDROID_ROOT/bionic/libc/include/android/versioning.h |

---
### 📅 2026-07-31

| Time | User & Message |
| :--- | :--- |
| 13:14:53 | *&lt;sharks_&gt;*: Okay - I'm trying to copy the work I did on X10IV to X10V, but as I don't own the phone @juz is helping me debug it remotely. I've sent him a few builds and we're at the stage where we can telnet in and have /vendor, /system, /product, /odm, etc. - all the things are mounted. But I'm getting a new error I've never seen before. |
| 13:14:57 | *&lt;sharks_&gt;*: Droid-hal-init is not working because eg. "droid-hal-init: mount("proc", "/proc", "proc", 0, "hidepid=2,gid=" MAKE_STR(AID_READPROC)) failed Device or resource busy" |
| 13:15:16 | *&lt;sharks_&gt;*: "droid-hal-init: mount("sysfs", "/sys", "sysfs", 0, NULL) failed Device or resource busy" |
| 13:15:31 | &lt;Mister_Magister&gt;: dhi shuoldn't be mounting anything so i think it's red herring |
| 13:15:35 | *&lt;sharks_&gt;*: What confuses me is that these directories are present on the running phone |
| 13:15:45 | &lt;Mister_Magister&gt;: yeah because dhi shouldn't be mounting them |
| 13:16:27 | *&lt;sharks_&gt;*: Dang, didn't expect a reply so quick! Okay, so that's not the fault then. Thanks |
| 13:16:46 | *&lt;sharks_&gt;*: Here's journal - anything else you can spot that's killing dhi? https://paste.opensuse.org/pastes/18797bb6dac7 |
| 13:19:56 | <mark>&lt;mal&gt;</mark>: sharks_: are you sure you applied all patches correctly? |
| 13:21:56 | *&lt;sharks_&gt;*: Nope, I've never done that before in my life. Are you talking about `hybris-patches/apply-patches.sh --mb`? I did not do that on X10IV. |
| 13:26:38 | *&lt;sharks_&gt;*: Certainly something preventing boot is `/dev/*binder` is not present on the device! |
| 13:42:17 | *&lt;sharks_&gt;*: apply-patches.sh seems to, among many other things, fix the `$ANDROID_ROOT/bionic/libc/include/android/versioning.h` issues I have been struggling with all week up until I manually copied the change from my X10IV sources yesterday |
| 13:42:25 | *&lt;sharks_&gt;*: Why did I not have to do all this for X10IV?? |
| 13:44:47 | <mark>&lt;mal&gt;</mark>: what? you didn't do that? |
| 13:45:27 | <mark>&lt;mal&gt;</mark>: how did you manage to get x10iv to boot without the patches |
| 13:46:42 | <mark>&lt;mal&gt;</mark>: btw, you should be able to use same source tree for both x10iv and v if those use same android version, just use different breakfast/lunch command before building |
| 13:47:01 | *&lt;sharks_&gt;*: Not only boot, it's fully functioning and has been my daily driver phone for weeks |
| 13:47:12 | <mark>&lt;mal&gt;</mark>: that makes no sense at all |
| 13:47:27 | <mark>&lt;mal&gt;</mark>: you must have applied patches at some point to x10iv |
| 13:47:40 | *&lt;sharks_&gt;*: I certainly don't recall doing it, but I suppose I must have |
| 13:47:50 | &lt;Mister_Magister&gt;: repo status :P |
| 13:48:36 | *&lt;sharks_&gt;*: Anyway - yes, I should be able to use the same source tree, you're right. But I need to understand how to handle the little differences eg. different dmsetup.sh value because X10IV has super on /dev/sda73 and X10V has super on /dev/sda74 |
| 13:48:52 | *&lt;sharks_&gt;*: At the minute I'm just trying to get X10V to boot, then I'll try and work out how to merge the sources |
| 13:48:57 | <mark>&lt;mal&gt;</mark>: those are droid-config, not related to android side build |
| 13:49:16 | <mark>&lt;mal&gt;</mark>: you of course have separate droid-config repo and other adaptation repos for the devices |
| 13:49:45 | <mark>&lt;mal&gt;</mark>: but android source build can be done using same repo synced source tree |
| 13:49:47 | *&lt;sharks_&gt;*: Oh? I thought Nagara had the same droid-config repo for both 1IV and 5IV? |
| 13:49:56 | <mark>&lt;mal&gt;</mark>: it does? |
| 13:50:02 | *&lt;sharks_&gt;*: I thought it did |
| 13:50:17 | <mark>&lt;mal&gt;</mark>: it seems it does |
| 13:50:26 | *&lt;sharks_&gt;*: Yeah |
| 13:51:26 | *&lt;sharks_&gt;*: I'm not 100% sure how they managed that yet, but I was going to try and copy it - as I say, if I can be sucessful at getting X10V booting. |
| 13:51:57 | *&lt;sharks_&gt;*: But I didn't want to break my X10IV source tree by cramming a bunch of X10V stuff into it right off the bat |
| 13:52:40 | <mark>&lt;mal&gt;</mark>: but x10iv and v are not same platform, murray vs zambezi |
| 13:53:03 | <mark>&lt;mal&gt;</mark>: those two nagara devices are both same platform |
| 13:53:31 | *&lt;sharks_&gt;*: Okay, so while X10IV and X10V are very very similar, they cannot use the same droid-config etc.? |
| 13:54:11 | *&lt;sharks_&gt;*: Even though they have the same sm6375 chipset and same kernel? |
| 13:54:15 | <mark>&lt;mal&gt;</mark>: maybe with all kinds of hacks in droid-config repo, not sure if it's worth the effort to try to use same droid-config |
| 13:55:05 | *&lt;sharks_&gt;*: Okay. I'll keep it apart then, seems like it might be above my pay grade to try those kinds of hacks. |

---
### 📅 2026-08-01

| Time | User & Message |
| :--- | :--- |
| 10:54:36 | *&lt;sharks_&gt;*: Hey all - I'm stuck again. On X10V, we can't get the GUI up. I have "droid-hal-init: Control message: Could not find 'android.hardware.graphics.composer@2.1::IComposer/default' for ctl.interface_start from pid: 1772 (/system/bin/hwservicemanager)" repeating in journal. On X10IV I solved this by fixing "evdevtouch" in `var/lib/environment/compositor/droid-hal-device.conf`, but that is not the problem on X10V and touchscreen is active at /d |
| 10:54:46 | *&lt;sharks_&gt;*: `test_hwcomposer` does nothing either |
| 10:54:52 | *&lt;sharks_&gt;*: But on X10IV it worked |
| 10:56:12 | *&lt;sharks_&gt;*: On both devices, `/vendor/etc/vintf/manifest/vendor.qti.hardware.display.composer-service.xml` contains android.hardware.graphics.composer 2.4 |
| 11:25:59 | *&lt;sharks_&gt;*: @juz has just uploaded the latest logcat and journal for me --> https://paste.opensuse.org/pastes/97dbdcf92f97 |
| 12:00:27 | *&lt;sharks_&gt;*: Ooh - linkerconfig! I missed a step! https://irclogs.sailfishos.org/logs/%23sailfishos-porters/2026/%23sailfishos-porters.2026-06-27.log.html#t2026-06-27T12:57:42 |
| 12:29:31 | *&lt;sharks_&gt;*: Woo! The thing booted! We got to the homescreen! I've ported a device I don't even own and have never laid eyes on! |
| 12:37:32 | *&lt;sharks_&gt;*: Alright, time to get serious, initial impressions show this thing is just as reliable as X10IV. @mal and/or @Keto, may I please have zambezi repositories set up in OBS? Over the next few days I'll turn this into an LVM image and get it shifted to OBS |
| 12:43:53 | *&lt;sharks_&gt;*: `ssu s` --> "Device Model: Xperia 10 V (xqdc72 / community)" |
| 18:21:05 | <mark>&lt;mal&gt;</mark>: sharks https://build.sailfishos.org/project/show/nemo:devel:hw:sony:zambezi and https://build.sailfishos.org/project/show/nemo:testing:hw:sony:zambezi |
| 18:22:35 | <mark>&lt;mal&gt;</mark>: for store access it might take some time until Keto checks IRC |
| 22:30:36 | *&lt;sharks__&gt;*: Thanks mal, tonight I will start work on getting things into OBS. Store access can wait, no problem |

---
### 📅 2026-08-02

| Time | User & Message |
| :--- | :--- |
| 08:50:54 | *&lt;sharks_&gt;*: Hey - has anyone got any experience debugging the headphone jack? Using the `Audio Output Chooser` app, we are able to manually redirect audio to the headphone jack, but Sailfish does not detect the presence of headphones and redirect audio automatically. The Headset Jack is present at `/dev/input/event6` and we see binary output garbage in that file on plugging and unpluging headphones. |
| 09:39:32 | **&lt;elros34&gt;**: use evdev_trace -t /dev/input/event6 for non garbage output |
| 09:51:20 | *&lt;sharks_&gt;*: Thanks, I didn't know about edev_trace. It seems the output from that is good --> https://paste.opensuse.org/pastes/3fe8750a05bd |
| 09:52:03 | *&lt;sharks_&gt;*: So I am confident that /dev/input/event* is working on both phones, but I don't know why Sailfish doesn't redirect audio on the 10V |
| 09:52:43 | *&lt;sharks_&gt;*: To clarify, that pastbin shows output of plugging in and then unplugging headphones on both devices |
| 09:53:27 | **&lt;elros34&gt;**: so maybe detection isn't broken. Don't you have other  event  devices which might get wrongly detected as a headphone? |
| 10:05:33 | *&lt;sharks_&gt;*: The event devices are slightly different between X10IV (https://paste.opensuse.org/pastes/856c1b84370e) and X10V (https://paste.opensuse.org/pastes/277f56faca9b) |
| 10:06:04 | *&lt;sharks_&gt;*: But I don't think anything could be wrongly detected? |
| 10:10:34 | **&lt;elros34&gt;**: you could use evdev_trace for this too. You can even check whether this node is used by dsme via evdev_trace -I |
| 10:11:31 | *&lt;sharks_&gt;*: Thanks elros, but juz has just informed me that it is in fact now working. It definitely wasn't this time yesterday, but he's restarted the phone since then so perhaps it was just a temporary hiccup on first boot? A bit like the issue we have with sensorfwd which doesn't work right on first boot. |
| 10:13:30 | **&lt;elros34&gt;**: next time try to restart ohmd to narrow the issue |
| 10:16:48 | **&lt;elros34&gt;**: for sensorfwd on one device I wait for udev device to appear but I guess you have binder sensors so this must be done differently. Maybe with binder-list |
| 10:17:07 | *&lt;sharks_&gt;*: Yeah we have aidl sensors |
| 10:17:45 | *&lt;sharks_&gt;*: I will ask juz to restart ohmd the next time he flashes and boots for the first time, but headphones seem to work across reboots for him now |
| 10:21:04 | **&lt;elros34&gt;**: maybe power down, power up instead reboot:P |
| 10:22:08 | **&lt;elros34&gt;**: But bugs on first boot are meh |
| 10:27:43 | *&lt;sharks_&gt;*: Yeah, I'm not too concerned about a couple of services being wonky on first boot. I've been using this X10IV as my only phone for weeks now and it's been rock solid since the second boot |
