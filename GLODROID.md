# Glodroid

Project page - https://glodroid.github.io/ 

## Setup and Build
* [Setup AOSP Build environment](./AOSP.md)
* Fetch and build
    ```
      mkdir -p GloDroid
      cd GloDroid
      repo init -u https://github.com/glodroid/glodroid_manifest
      repo sync -cq
      source ./build/envsetup.sh
      lunch rpi4-userdebug
      make images
    ```
**NOTE**

The initial sync will take a lot of time and the build will fail with `OOM` error during the initial setup of `soong`. Keep running the `make images` command till the step is succefully completed.
Also play with `-j<concurrent-tasks>` flag with `make` command to reduce the load. For e.g `make -j3 images`.

## Flashing
**Requires Ubuntu machine**

On successfull build, `images.tar.gz` file will be generated and is available at `out/target/product/rpi4` directory from source root.
The contents of `images.tar.gz` are as follows
```
images
├── adb
├── boot.img
├── boot_dtbo.img
├── bootloader-sd.img
├── deploy-gpt.img
├── deploy-sd.img
├── env.img
├── fastboot
├── flash-sd.sh
├── mke2fs
└── super.img
```
### Prepare SD card and Flash
* Extract the content of `images.tar.gz` to a folder and make `adb`, `fastboot` and `flash-sd.sh` files executable with `chmod +x adb fastboot flash-sd.sh`
* Use image flashing tools like [etcher](https://www.balena.io/etcher/) and flash `deploy-sd.img` file on a SD card (Min 16GB)
* Insert the SD card to RPI4, Connect Keyboard, Monitor to RPI4.
* Connect RPI4 to PC/Laptop over USB (Connect to USB-C Port on PI)
* On Boot-up and confirm `fastboot` command shows the connected 
  ```
  ./adb devices
  ./fastboot devices 
  $ sudo ./fastboot devices
  10000000a0bb32ca	Android Fastboot
  ```
* Start the flash with `sudo ./flash-sd.sh` . The process will take several minutes and pi would reboot multiple times. 

**NOTE**

Do not interact with the PI during the flashing with keyboards. Have patience and take a break.

A succesfull flash should get the `android` launch screen. The initial boot up would take some time and a black screen is expected for sometime.


Sample flash output.

```
~/gdroid$ sudo ./flash-sd.sh
+ ./fastboot flash gpt deploy-gpt.img
Sending 'gpt' (128 KB)                             OKAY [  1.269s]
Writing 'gpt'                                      OKAY [  0.084s]
Finished. Total time: 1.456s
+ ./fastboot flash bootloader bootloader-sd.img
Invalid sparse file format at header magic
Sending sparse 'bootloader' 1/1 (8916 KB)          OKAY [ 85.424s]
Writing 'bootloader'                               OKAY [ 22.550s]
Finished. Total time: 109.758s
+ ./fastboot flash uboot-env env.img
Sending 'uboot-env' (256 KB)                       OKAY [  2.494s]
Writing 'uboot-env'                                OKAY [  0.070s]
Finished. Total time: 2.713s
+ ./fastboot reboot
Rebooting                                          OKAY [  0.024s]
Finished. Total time: 0.126s
+ set +x
+ ./fastboot oem format
< waiting for any device >
OKAY [  0.075s]
Finished. Total time: 0.075s
+ ./fastboot flash bootloader bootloader-sd.img
Invalid sparse file format at header magic
Sending sparse 'bootloader' 1/1 (8916 KB)          OKAY [ 85.424s]
Writing 'bootloader'                               OKAY [ 22.630s]
Finished. Total time: 108.541s
+ ./fastboot flash uboot-env env.img
Sending 'uboot-env' (256 KB)                       OKAY [  2.494s]
Writing 'uboot-env'                                OKAY [  0.102s]
Finished. Total time: 2.681s
+ ./fastboot flash recovery_boot boot.img
Invalid sparse file format at header magic
Sending sparse 'recovery_boot' 1/2 (16380 KB)      OKAY [156.875s]
Writing 'recovery_boot'                            OKAY [  2.400s]
Sending sparse 'recovery_boot' 2/2 (9224 KB)       OKAY [ 88.307s]
Writing 'recovery_boot'                            OKAY [  1.961s]
Finished. Total time: 250.082s
+ ./fastboot erase misc
Erasing 'misc'                                     OKAY [  0.039s]
Finished. Total time: 0.176s
+ ./fastboot reboot-fastboot
Rebooting into fastboot                            OKAY [  0.076s]
< waiting for any device >
Finished. Total time: 19.756s
+ ./fastboot flash boot boot.img
Sending 'boot' (32768 KB)                          OKAY [  1.646s]
Writing 'boot'                                     OKAY [  4.758s]
Finished. Total time: 6.706s
+ ./fastboot flash dtbo_a boot_dtbo.img
Sending 'dtbo_a' (49 KB)                           OKAY [  0.013s]
Writing 'dtbo_a'                                   OKAY [  0.054s]
Finished. Total time: 0.137s
+ ./fastboot flash super super.img
Invalid sparse file format at header magic
Sending sparse 'super' 1/6 (262136 KB)             OKAY [ 12.736s]
Writing 'super'                                    OKAY [ 44.650s]
Sending sparse 'super' 2/6 (262120 KB)             OKAY [ 12.412s]
Writing 'super'                                    OKAY [ 43.035s]
Sending sparse 'super' 3/6 (259892 KB)             OKAY [ 12.408s]
Writing 'super'                                    OKAY [ 42.990s]
Sending sparse 'super' 4/6 (238732 KB)             OKAY [ 11.620s]
Writing 'super'                                    OKAY [ 39.853s]
Sending sparse 'super' 5/6 (259096 KB)             OKAY [ 13.129s]
Writing 'super'                                    OKAY [ 43.437s]
Sending sparse 'super' 6/6 (128732 KB)             OKAY [  5.774s]
Writing 'super'                                    OKAY [ 89.493s]
Finished. Total time: 394.434s
+ ./fastboot format:ext4 userdata
Warning: userdata type is raw, but ext4 was requested for formatting.
mke2fs 1.45.4 (23-Sep-2019)
Creating filesystem with 3373051 4k blocks and 843776 inodes
Filesystem UUID: ac898a74-a800-4fa3-99f3-82df119e46b2
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done

Sending 'userdata' (4288 KB)                       OKAY [  0.213s]
Writing 'userdata'                                 OKAY [  2.623s]
Finished. Total time: 3.150s
+ ./fastboot format:ext4 metadata
Warning: metadata type is raw, but ext4 was requested for formatting.
mke2fs 1.45.4 (23-Sep-2019)
Creating filesystem with 4096 4k blocks and 4096 inodes

Allocating group tables: done
Writing inode tables: done
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done

Sending 'metadata' (52 KB)                         OKAY [  0.010s]
Writing 'metadata'                                 OKAY [  0.115s]
Finished. Total time: 0.198s
+ ./fastboot reboot
Rebooting                                          OKAY [  0.005s]
Finished. Total time: 0.056s
```

## Tweaking build

RPI4 related make files is available at `device/target/glodroid/rpi4/`, tweak `rpi4.mk` to customize further.


## Adb remount
```
adb root
adb disable-verity
adb reboot
adb root
adb remount
```



