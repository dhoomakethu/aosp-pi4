# AOSP Build setup

## System requirements
* Ubuntu 18.04 or above
* Min 250GB Disk space and 16GB or more RAM. More the CORE faster the builds.
* Install required packages 
    * Required
        ```
            sudo apt-get install -y git-core gnupg flex bison build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 libncurses5 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig python3 python3-dev
        ```
        ```
            sudo apt-get install -y python3-pip pkg-config python3-dev ninja-build
            sudo pip3 install mako meson jinja2 ply
        ```
    * Optional
        `sudo apt-get install -y swig libssl-dev flex bison device-tree-compiler mtools git gettext libncurses5 libgmp-dev libmpc-dev cpio rsync dosfstools kmod gdisk`

For more info Refer https://source.android.com/setup/build/initializing

## Misc info

### To create a dist image
`make dist DIST_OUT=<path to dist dir>`

### To create OTA package
```
export PATH=out/host/linux-x86/bin/:system/extras/ext4_utils/:$PATH
ota_from_target_files <PATH to dist dir>/<PRODUCT>-target_files-<BUILD-TYPE>.ubuntu.zip ota_update.zip
```
Where `<PATH to dist dir>` is the path passed to `DIST_OUT` with `make dist` command or `out/target/product/<product>/obj/PACKAGING/target_files_intermediates` , `<PRODUCT>` is target used with `lunch` command

Refer https://source.android.com/devices/tech/ota/tools

### Test keys

By default `test keys` are used for signing the apk's and images (available under `build/target/product/security` refer https://source.android.com/devices/tech/ota/sign_builds to create and change the keys.

To sign an apk with the keys.
`JAVA_HOME=<JAVA-HOME-pATH>  <ANDROID_SDK_HOME>/build-tools/32.0.0/apksigner sign --key keys/platform.pk8 --cert keys/platform.x509.pem <my-apk>.apk`

Install with

```
adb install <my-apk>.apk
```