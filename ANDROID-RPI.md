# Glodroid

Project page - https://glodroid.github.io/ 

## Setup and Build
* [Setup AOSP Build environment](./AOSP.md)
* Fetch and build source
    ```
      mkdir -p android-rpi
      cd android-rpi
      repo init -u https://android.googlesource.com/platform/manifest -b android-12.1.0_r21
      git clone https://github.com/android-rpi/local_manifests .repo/local_manifests -b arpi-12
      repo sync
      source ./build/envsetup.sh
      lunch rpi4-eng
      make ramdisk systemimage vendorimage
    ```
  Outputs are available under out/target/product/rpi4/
* Fetch and build kernel (Outside AOSP source directory)
    ```
    mkdir -p android-kernel
    cd android-kernel
    repo init -u https://github.com/android-rpi/kernel_manifest -b arpi-5.10
    repo sync
    build/build.sh
    ```
   This generates the following files under out/arpi-5.10/dist/
   ```
    Image.gz
    bcm2711-rpi-*.dtb
    vc4-kms-v3d-pi4.dtbo
   ```
  
**NOTE**

The initial sync will take a lot of time and the build will fail with `OOM` error during the initial setup of `soong`. Keep running the `make ramdisk systemimage vendorimage` command till the step is succefully completed.
Also play with `-j<concurrent-tasks>` flag with `make` command to reduce the load. For e.g `make -j3 make ramdisk systemimage vendorimage`.

## Create Image for flashing
* Copy script [mkimg.sh](./mkimg.sh) to `AOSP_ROOT_DIRECTORY`.
* Update variable `KERNEL_OUT_DIR` in the script to point to the kernel directory above
* Run scirpt `./mkimg.sh` `sudo` if required. This creates an image file `arpi-12-<date>.img`. Where date is the current date in`YYYYMMDD` format.
* Zip the image created `zip arpi-12-<date>.img arpi-12-<date>.zip`

## Flashing
**Requires Ubuntu machine**

### Prepare SD card and Flash
* Use image flashing tools like [etcher](https://www.balena.io/etcher/) and flash `arpi-12-<date>.zip` generated above on to a SD card (Min 16GB)
* Insert the SD card to RPI4, Connect Keyboard, Monitor to RPI4.
* Connect RPI4 to PC/Laptop over USB (Connect to USB-C Port on PI)
* Wait for the device to boot!!!

## Adb remount
```
adb root
adb disable-verity
adb reboot
adb root
adb remount
```



