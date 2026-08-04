# Notes.md

As I do not own this phone, it is a bit of a "challenge" to debug things.

I've copied some of the various outputs of commands that @juz sent me while we were debugging and pasted them here. They might come in handy if I need to ever check for differences on the 10V compared to the 10IV that I own.

Other key differences that mattered for making this port were

|  | X10IV | X10V |
|---|-------|------|
| super | sda73 | sda74 |
| boot | sda31 | sda29 |
| mkfstab_skip_entries | (initial list) | Also required '/' |'


---------------------------------

<pre>
/lib/modules/5.4.300-qgki-g596e6ae2bc41 # ls
adsp_loader_dlkm.ko 
modules.builtin.bin 
rx_macro_dlkm.ko
apr_dlkm.ko 
modules.dep 
sec_ts_drv.ko
aw882xx_dlkm.ko 
modules.dep.bin 
snd_event_dlkm.ko
bolero_cdc_dlkm.ko 
modules.devname 
snx0.ko
bt_fm_slim.ko 
modules.order 
stub_dlkm.ko
btpower.ko 
modules.softdep 
swr_ctrl_dlkm.ko
bu520x1nvx.ko 
modules.symbols 
swr_dlkm.ko
camera.ko 
modules.symbols.bin 
tx_macro_dlkm.ko
ebtable_nat.ko 
native_dlkm.ko 
va_macro_dlkm.ko
ebtables.ko 
p73.ko 
wcd937x_dlkm.ko
ec617_drv.ko 
pinctrl_lpi_dlkm.ko 
wcd937x_slave_dlkm.ko
focaltech_fts.ko 
platform_dlkm.ko 
wcd938x_dlkm.ko
haptic.ko q6_dlkm.ko 
wcd938x_slave_dlkm.ko
leds-aw2016.ko 
q6_notifier_dlkm.ko 
wcd9xxx_dlkm.ko
machine_dlkm.ko 
q6_pdr_dlkm.ko 
wcd_core_dlkm.ko
mbhc_dlkm.ko 
rdbg.ko 
wlan.ko
modules.alias 
rmnet_core.ko 
wsa881x_analog_dlkm.ko
modules.alias.bin 
rmnet_ctl.ko 
xt_addrtype.ko
modules.builtin 
rmnet_offload.ko
modules.builtin.alias.bin 
rmnet_shs.ko
</pre>
---------------------------------------

Kernel cmdline:

<pre>
cgroup_disable=pressure log_buf_len=256K rcupdate.rcu_expedited=1 rcu_nocbs=0-7 kpti=off androidboot.hardware=qcom androidboot.memcg=1 androidboot.usbcontroller=4e00000.dwc3 cgroup.memory=nokmem,nosocket loop.max_part=7 lpm_levels.sleep_disabled=1 msm_rtb.filter=0x237 pcie_ports=compat service_locator.enable=1 swiotlb=0 ip6table_raw.raw_before_defrag=1 iptable_raw.raw_before_defrag=1 androidboot.hardware=qcom androidboot.memcg=1 androidboot.usbcontroller=4e00000.dwc3 cgroup.memory=nokmem,nosocket loop.max_part=7 lpm_levels.sleep_disabled=1 msm_rtb.filter=0x237 pcie_ports=compat service_locator.enable=1 swiotlb=0 ip6table_raw.raw_before_defrag=1 iptable_raw.raw_before_defrag=1 androidboot.verifiedbootstate=orange androidboot.keymaster=1 androidboot.bootdevice=4804000.ufshc androidboot.fstab_suffix=default androidboot.boot_devices=soc/4804000.ufshc androidboot.baseband=msm msm_drm.dsi_display0=qcom,mdss_dsi_samsung_fhd_amoled_cmd: androidboot.slot_suffix=_a rootwait ro init=/init androidboot.dtbo_idx=0 androidboot.dtb_idx=0 androidboot.force_normal_boot=1 androidboot.bootloader=6375-0005_X_Boot_SM6375_LA2.0_T_20 androidboot.serialno=SERIAL
startup=0x00008004 warmboot=0x77665501 oemandroidboot.babe09a9=00 androidboot.hardware.sku=c001707 androidboot.hardware.color=162 androidboot.serialno=SERIAL oemandroidboot.imei=IMEI
oemandroidboot.securityflags=0x00000003
</pre>

----------------------------------------

Dynamic Partitions:

<pre>
total 0
drwxr-xr-x 2 root root 300 Oct 14 2025 .
drwxr-xr-x 24 root root 9440 Jun 12 15:30 …
crw------- 1 root root 10, 236 Jun 12 15:29 control
brw-rw---- 1 root disk 253, 0 Oct 14 2025 dynpart-odm_a
brw-rw---- 1 root disk 253, 6 Oct 14 2025 dynpart-odm_a-cow
brw-rw---- 1 root disk 253, 1 Oct 14 2025 dynpart-product_a
brw-rw---- 1 root disk 253, 7 Oct 14 2025 dynpart-product_a-cow
brw-rw---- 1 root disk 253, 2 Oct 14 2025 dynpart-system_a
brw-rw---- 1 root disk 253, 8 Oct 14 2025 dynpart-system_a-cow
brw-rw---- 1 root disk 253, 3 Oct 14 2025 dynpart-system_ext_a
brw-rw---- 1 root disk 253, 9 Oct 14 2025 dynpart-system_ext_a-cow
brw-rw---- 1 root disk 253, 4 Oct 14 2025 dynpart-vendor_a
brw-rw---- 1 root disk 253, 10 Oct 14 2025 dynpart-vendor_a-cow
brw-rw---- 1 root disk 253, 5 Oct 14 2025 dynpart-vendor_dlkm_a
brw-rw---- 1 root disk 253, 11 Oct 14 2025 dynpart-vendor_dlkm_a-cow
| | |-/sys/fs/cgroup/systemd cgroup cgroup rw,nosuid,nodev,noexec,relatime,xattr,name=systemd
|-/odm /dev/mapper/dynpart-odm_a ext4 ro,relatime,seclabel
|-/vendor /dev/mapper/dynpart-vendor_a ext4 ro,relatime,seclabel
| |-/vendor/lib64/hw/audio.primary.default.so /dev/sda75[/.stowaways/sailfishos/usr/libexec/droid-hybris/system/lib64/hw/audio.hidl_compat.default.so] ext4 rw,noatime,seclabel,stripe=2
| |-/vendor/firmware_mnt /dev/sda33 vfat ro,relatime,uid=1000,gid=1000,fmask=0337,dmask=0227,codepage=437,iocharset=iso8859-1,shortname=lower,errors=remount-ro
| |-/vendor/bt_firmware /dev/sda31 vfat ro,relatime,uid=1002,gid=3002,fmask=0337,dmask=0227,codepage=437,iocharset=iso8859-1,shortname=lower,errors=remount-ro
| `-/vendor/dsp /dev/sda35 ext4 ro,nosuid,nodev,relatime,seclabel
|-/system_ext /dev/mapper/dynpart-system_ext_a ext4 ro,relatime,seclabel
|-/product /dev/mapper/dynpart-product_a ext4 ro,relatime,seclabel,discard
|-/vendor_dlkm /dev/mapper/dynpart-vendor_dlkm_a ext4 ro,relatime,seclabel
|-/system_root /dev/mapper/dynpart-system_a ext4 ro,relatime,seclabel
|-/system /dev/mapper/dynpart-system_a[/system] ext4 ro,relatime,seclabel
|-/mnt/vendor/persist /dev/sda2 ext4 rw,nosuid,nodev,noatime,seclabel
</pre>

----------------------------------------

<pre>
# ls /vendor/etc/vintf/manifest/

android.hardware.biometrics.fingerprint-service.lineage.xml
android.hardware.camera.provider-service.lineage.xml
android.hardware.cas@1.2-service.xml
android.hardware.drm-service.clearkey.xml
android.hardware.gnss@2.1-service-qti.xml
android.hardware.graphics.mapper-impl-qti-display.xml
android.hardware.health-service.qti.xml
android.hardware.sensors-multihal.xml
android.hardware.thermal-service.qti.xml
android.hardware.usb-service.qti.xml
android.hardware.usb.gadget-service.qti.xml
android.hardware.wifi-service.xml
android.hardware.wifi.hostapd.xml
android.hardware.wifi.supplicant.xml
bluetooth_audio.xml
boot-service.qti.xml
c2_manifest_vendor.xml
manifest_android.hardware.drm@1.3-service.widevine.xml
memtrack_qti.xml
nfc-service-nxp.xml
power.xml
secure_element-service-nxp.xml
vendor.lineage.health-service.default.xml
vendor.lineage.livedisplay-service.sony.xml
vendor.qti.gnss@4.0-service.xml
vendor.qti.hardware.display.allocator-service.xml
vendor.qti.hardware.display.composer-service.xml
vendor.qti.hardware.servicetracker@1.2-service.xml
vendor.qti.hardware.vibrator.service.xml
vendor.semc.system.idd.manifest.xml
vendor.somc.hardware.aidlmiscta-somc.xml
</pre>

----------------------------------------

<pre>
# lsmod
Module Size Used by
wlan 9527296 0
wsa881x_analog_dlkm 53248 0
wcd938x_slave_dlkm 24576 0
wcd937x_slave_dlkm 24576 0
va_macro_dlkm 135168 0
tx_macro_dlkm 155648 0
stub_dlkm 24576 0
sec_ts_drv 483328 0
rx_macro_dlkm 139264 0
swr_ctrl_dlkm 61440 3 tx_macro_dlkm,rx_macro_dlkm,va_macro_dlkm
rmnet_shs 163840 0
rmnet_offload 32768 0
rmnet_core 217088 3 rmnet_offload,rmnet_shs
rmnet_ctl 28672 1 rmnet_core
rdbg 36864 0
pinctrl_lpi_dlkm 32768 0
p73 36864 0
native_dlkm 200704 0
platform_dlkm 3543040 1 native_dlkm
machine_dlkm 196608 1 platform_dlkm
wcd938x_dlkm 147456 1 machine_dlkm
wcd937x_dlkm 131072 1 machine_dlkm
mbhc_dlkm 61440 2 wcd937x_dlkm,wcd938x_dlkm
wcd9xxx_dlkm 49152 2 wcd937x_dlkm,wcd938x_dlkm
wcd_core_dlkm 45056 7 machine_dlkm,wcd937x_dlkm,tx_macro_dlkm,rx_macro_dlkm,va_macro_dlkm,wcd938x_dlkm,wsa881x_analog_dlkm
swr_dlkm 40960 5 wcd937x_slave_dlkm,wcd937x_dlkm,wcd938x_dlkm,swr_ctrl_dlkm,wcd938x_slave_dlkm
leds_aw2016 32768 0
haptic 204800 0
ec617_drv 45056 0
bu520x1nvx 24576 0
bt_fm_slim 32768 0
btpower 40960 1 bt_fm_slim
bolero_cdc_dlkm 73728 4 machine_dlkm,tx_macro_dlkm,rx_macro_dlkm,va_macro_dlkm
aw882xx_dlkm 208896 0
q6_dlkm 1675264 9 bolero_cdc_dlkm,machine_dlkm,pinctrl_lpi_dlkm,va_macro_dlkm,aw882xx_dlkm,swr_ctrl_dlkm,wcd9xxx_dlkm,native_dlkm,platform_dlkm
adsp_loader_dlkm 24576 0
apr_dlkm 245760 3 q6_dlkm,adsp_loader_dlkm,platform_dlkm
q6_notifier_dlkm 24576 2 pinctrl_lpi_dlkm,apr_dlkm
q6_pdr_dlkm 24576 1 q6_notifier_dlkm
snd_event_dlkm 24576 5 bolero_cdc_dlkm,machine_dlkm,q6_dlkm,pinctrl_lpi_dlkm,apr_dlkm
focaltech_fts 225280 0
snx0 40960 1 p73
camera 3977216 0
</pre>

----------------------------------------

<pre>
[defaultuser@Xperia10V ~] cat /sys/class/sound/card0/id 
holiqrdsku1sndc
</pre>

----------------------------------------

<pre>
juz@laptop:~/platform_system_tools_mkbootimg$ python3 unpack_bootimg.py --boot_img ~/pdx235/Lineage/boot.img --out img/
boot magic: ANDROID!
kernel_size: 39145984
ramdisk size: 19583167
os version: 15.0.0
os patch level: 2025-10
boot image header version: 3
command line args:
</pre>

----------------------------------------

<pre>
[defaultuser@Xperia10V ~] cat /sys/firmware/devicetree/base/model 
Sony Mobile Communications. PDX235(BLAIR v1)
</pre>
----------------------------------------

<pre>
[defaultuser@Xperia10V ~] binder-list -d /dev/hwbinder
android.hardware.audio@6.0::IDevicesFactory/default
</pre>

<mark>MISSING ANY MORE INFORMATION -- PLEASE IF YOU ARE READING THIS AND RUNNING THIS PORT ON A 10V, PROVIDE UPDATED OUTPUT!</mark>

----------------------------------------

<pre>
[defaultuser@Xperia10V ~] binder-list -d /dev/binder
</pre>
<mark>MISSING ANY MORE INFORMATION -- PLEASE IF YOU ARE READING THIS AND RUNNING THIS PORT ON A 10V, PROVIDE UPDATED OUTPUT!</mark>

----------------------------------------

<pre>
[defaultuser@Xperia10V ~] cat /proc/bus/input/devices

I: Bus=0000 Vendor=0000 Product=0000 Version=0000
N: Name="qpnp_pon"
P: Phys=qpnp_pon/input0
S: Sysfs=/devices/platform/soc/1c40000.qcom,spmi/spmi-0/spmi0-00/1c40000.qcom,spmi:qcom,pm6125@0:qcom,power-on@800/input/input0
U: Uniq=
H: Handlers=kbd event0 cpufreq
B: PROP=0
B: EV=3
B: KEY=18000000000000 0

I: Bus=0000 Vendor=0000 Product=0000 Version=0000
N: Name="SOMC Charger Removal"
P: Phys=
S: Sysfs=/devices/platform/soc/1c40000.qcom,spmi/spmi-0/spmi0-02/1c40000.qcom,spmi:qcom,pm7250b@2:qcom,qpnp-smb5/input/input1
U: Uniq=
H: Handlers=kbd event1 cpufreq
B: PROP=0
B: EV=3
B: KEY=4 0 0 0

I: Bus=0019 Vendor=0001 Product=0001 Version=0100
N: Name="gpio-keys"
P: Phys=gpio-keys/input0
S: Sysfs=/devices/platform/soc/soc:gpio_keys/input/input2
U: Uniq=
H: Handlers=kbd event2 cpufreq
B: PROP=0
B: EV=3
B: KEY=4000000000000 0

I: Bus=0018 Vendor=0000 Product=0000 Version=0000
N: Name="fts_ts"
P: Phys=
S: Sysfs=/devices/platform/soc/4c88000.i2c/i2c-3/3-0038/input/input3
U: Uniq=
H: Handlers=event3 cpufreq kgsl
B: PROP=2
B: EV=b
B: KEY=400 0 0 0 10168000000000 4d04081460000
B: ABS=661800000000000

I: Bus=0000 Vendor=0000 Product=0000 Version=0000
N: Name="egis_fp"
P: Phys=
S: Sysfs=/devices/virtual/input/input4
U: Uniq=
H: Handlers=kbd event4 cpufreq
B: PROP=0
B: EV=3
B: KEY=4000000000000000 800 37c000000000 0
</pre>

<mark>MISSING ANY MORE INFORMATION -- PLEASE IF YOU ARE READING THIS AND RUNNING THIS PORT ON A 10V, PROVIDE UPDATED OUTPUT!</mark>

---------------------------------------
/vendor/etc/vintf/manifest.xml

<pre>
<manifest version="9.0" type="device" target-level="6">
    <hal format="hidl">
        <name>android.hardware.audio</name>
        <transport>hwbinder</transport>
        <fqname>@6.0::IDevicesFactory/default</fqname>
    </hal>
    <hal format="hidl">
        <name>android.hardware.audio.effect</name>
        <transport>hwbinder</transport>
        <fqname>@6.0::IEffectsFactory/default</fqname>
    </hal>
    <hal format="hidl">
        <name>android.hardware.bluetooth</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IBluetoothHci/default</fqname>
    </hal>
    <hal format="hidl">
        <name>android.hardware.gatekeeper</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IGatekeeper/default</fqname>
    </hal>
    <hal format="hidl">
        <name>android.hardware.keymaster</name>
        <transport>hwbinder</transport>
        <fqname>@4.1::IKeymasterDevice/default</fqname>
    </hal>
    <hal format="hidl">
        <name>android.hardware.radio</name>
        <transport>hwbinder</transport>
        <fqname>@1.2::ISap/slot1</fqname>
        <fqname>@1.2::ISap/slot2</fqname>
        <fqname>@1.5::IRadio/slot1</fqname>
        <fqname>@1.5::IRadio/slot2</fqname>
    </hal>
    <hal format="hidl">
        <name>android.hardware.radio.config</name>
        <transport>hwbinder</transport>
        <fqname>@1.1::IRadioConfig/default</fqname>
    </hal>
    <hal format="hidl">
        <name>android.hardware.secure_element</name>
        <transport>hwbinder</transport>
        <fqname>@1.2::ISecureElement/SIM1</fqname>
        <fqname>@1.2::ISecureElement/SIM2</fqname>
    </hal>
    <hal format="hidl">
        <name>android.hardware.soundtrigger</name>
        <transport>hwbinder</transport>
        <fqname>@2.3::ISoundTriggerHw/default</fqname>
    </hal>
    <hal format="hidl">
        <name>android.hardware.tetheroffload.config</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IOffloadConfig/default</fqname>
    </hal>
    <hal format="hidl">
        <name>android.hardware.tetheroffload.control</name>
        <transport>hwbinder</transport>
        <fqname>@1.1::IOffloadControl/default</fqname>
    </hal>
    <hal format="hidl">
        <name>com.qualcomm.qti.dpm.api</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IdpmQmi/dpmQmiService</fqname>
    </hal>
    <hal format="hidl">
        <name>com.qualcomm.qti.imscmservice</name>
        <transport>hwbinder</transport>
        <fqname>@2.2::IImsCmService/qti.ims.connectionmanagerservice</fqname>
    </hal>
    <hal format="hidl">
        <name>com.qualcomm.qti.uceservice</name>
        <transport>hwbinder</transport>
        <fqname>@2.3::IUceService/com.qualcomm.qti.uceservice</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.egistec.hardware.fingerprint</name>
        <transport>hwbinder</transport>
        <fqname>@4.0::IBiometricsFingerprintBix/default</fqname>
        <fqname>@4.0::IBiometricsFingerprintRbs/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.data.factory</name>
        <transport>hwbinder</transport>
        <fqname>@2.2::IFactory/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.diaghal</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::Idiag/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.esepowermanager</name>
        <transport>hwbinder</transport>
        <fqname>@1.1::IEsePowerManager/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.bluetooth_audio</name>
        <transport>hwbinder</transport>
        <fqname>@2.0::IBluetoothAudioProvidersFactory/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.bluetooth_sar</name>
        <transport>hwbinder</transport>
        <fqname>@1.1::IBluetoothSar/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.btconfigstore</name>
        <transport>hwbinder</transport>
        <fqname>@2.0::IBTConfigStore/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.cacert</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IService/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.camera.postproc</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IPostProcService/camerapostprocservice</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.capabilityconfigstore</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::ICapabilityConfigStore/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.data.connection</name>
        <transport>hwbinder</transport>
        <fqname>@1.1::IDataConnection/slot1</fqname>
        <fqname>@1.1::IDataConnection/slot2</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.data.iwlan</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IIWlan/slot1</fqname>
        <fqname>@1.0::IIWlan/slot2</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.data.latency</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::ILinkLatency/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.dsp</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IDspService/dspservice</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.fm</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IFmHci/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.perf</name>
        <transport>hwbinder</transport>
        <fqname>@2.2::IPerf/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.qseecom</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IQSEECom/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.qteeconnector</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IAppConnector/default</fqname>
        <fqname>@1.0::IGPAppConnector/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.radio.am</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IQcRilAudio/slot1</fqname>
        <fqname>@1.0::IQcRilAudio/slot2</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.radio.ims</name>
        <transport>hwbinder</transport>
        <fqname>@1.7::IImsRadio/imsradio0</fqname>
        <fqname>@1.7::IImsRadio/imsradio1</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.radio.internal.deviceinfo</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IDeviceInfo/deviceinfo</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.radio.lpa</name>
        <transport>hwbinder</transport>
        <fqname>@1.1::IUimLpa/UimLpa0</fqname>
        <fqname>@1.1::IUimLpa/UimLpa1</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.radio.qcrilhook</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IQtiOemHook/oemhook0</fqname>
        <fqname>@1.0::IQtiOemHook/oemhook1</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.radio.qtiradio</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IQtiRadio/slot1</fqname>
        <fqname>@1.0::IQtiRadio/slot2</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.radio.qtiradio</name>
        <transport>hwbinder</transport>
        <fqname>@2.7::IQtiRadio/slot1</fqname>
        <fqname>@2.7::IQtiRadio/slot2</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.radio.uim</name>
        <transport>hwbinder</transport>
        <fqname>@1.2::IUim/Uim0</fqname>
        <fqname>@1.2::IUim/Uim1</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.radio.uim_remote_client</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IUimRemoteServiceClient/uimRemoteClient0</fqname>
        <fqname>@1.0::IUimRemoteServiceClient/uimRemoteClient1</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.radio.uim_remote_server</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IUimRemoteServiceServer/uimRemoteServer0</fqname>
        <fqname>@1.0::IUimRemoteServiceServer/uimRemoteServer1</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.sensorscalibrate</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::ISensorsCalibrate/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.soter</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::ISoter/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.tui_comm</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::ITuiComm/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.hardware.wifidisplaysession</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IWifiDisplaySession/wifidisplaysession</fqname>
        <fqname>@1.0::IWifiDisplaySessionAudioTrack/wifidisplaysessionaudiotrack</fqname>
        <fqname>@1.0::IWifiDisplaySessionImageTrack/wifidisplaysessionimagetrack</fqname>
        <fqname>@1.0::IWifiDisplaySessionVideoTrack/wifidisplaysessionvideotrack</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.ims.callinfo</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IService/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.ims.factory</name>
        <transport>hwbinder</transport>
        <fqname>@1.1::IImsFactory/default</fqname>
        <fqname>@2.2::IImsFactory/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.imsrtpservice</name>
        <transport>hwbinder</transport>
        <fqname>@3.0::IRTPService/imsrtpservice</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.qti.qspmhal</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IQspmhal/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.semc.hardware.display</name>
        <transport>hwbinder</transport>
        <fqname>@2.2::IDisplay/default</fqname>
        <fqname>@2.2::IFramerateController/default</fqname>
    </hal>
    <hal format="aidl">
        <name>vendor.semc.hardware.spc</name>
        <fqname>ISpc/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.somc.hardware.miscta</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::IMisctaGlobal/default</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.somc.hardware.radio</name>
        <transport>hwbinder</transport>
        <fqname>@1.0::ISomcHook/somchook</fqname>
        <fqname>@1.0::ISomcHook/somchook2</fqname>
    </hal>
    <hal format="hidl">
        <name>vendor.somc.hardware.security.secd</name>
        <transport>hwbinder</transport>
        <fqname>@1.1::IDeviceSecurity/default</fqname>
    </hal>
    <sepolicy>
        <version>202404</version>
    </sepolicy>
</manifest> 
</pre>
