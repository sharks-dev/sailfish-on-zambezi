# sailfish-on-zambezi

LineageOS 22.2 based SailfishOS for Sony Xperia 10 V

Heavily based on [sailfish-on-murray](https://github.com/sharks-dev/sailfish-on-murray)

## Refer to:

https://github.com/sharks-dev/droid-config-pdx235

https://github.com/sharks-dev/android_kernel_sony_sm6375

https://github.com/sharks-dev/droid-hal-version-pdx225

https://github.com/sharks-dev/droid-hal-pdx225

https://github.com/sharks-dev/droid-bthelper

And for LVM image / OBS:

https://github.com/sharks-dev/droid-hal-img-boot-sony-xqdc72

https://github.com/sharks-dev/community-adaptation-xqdc72

## Getting Lineage

It seems that lineageos.org do not host older versions of Lineage for this device(?)

The latest build of Lineage 22.2 is available from the timschumi.net mirror at this URL (note: boot.img, vbmeta.img and dtbo.img are not available here): https://lineage-archive.timschumi.net/build/36086 

The remaining files are available on the Wayback machine at these URLs: 
 - vbmeta: https://web.archive.org/web/20251113004137/https://mirrors.ocf.berkeley.edu/lineageos/full/pdx235/20251010/vbmeta.img
 - dtbo: https://web.archive.org/web/20251031202406/https://mirror.kumi.systems/lineageos/full/pdx235/20251010/dtbo.img
 - boot: https://web.archive.org/web/20251031194403/https://gemmei.ftp.acc.umu.se/mirror/lineageos/full/pdx235/20251010/boot.img
 - copy-partitions: https://web.archive.org/web/20250328093758/https://mirrors.ocf.berkeley.edu/lineageos/tools/copy-partitions-20220613-signed.zip

The flashing instructions can also be found on Wayback: https://web.archive.org/web/20250418165834/https://wiki.lineageos.org/devices/pdx235/install/#

## Notes:

See [sailfish-on-murray](https://github.com/sharks-dev/sailfish-on-murray) for further details including building from sources, building devel images, and installing the testing image. This port shares a great deal with that one.

## Acknowledgements

Thanks to @juz for his continued assistance testing and debugging this port.
