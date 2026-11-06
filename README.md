### How to build:

1. Establish [Android build environment](https://source.android.com/setup/initializing) and install [repo](https://source.android.com/docs/setup/develop#installing-repo).

2. Initialize repo:

```
repo init -u https://android.googlesource.com/kernel/manifest -b common-android17-6.18-lts
curl -o .repo/local_manifests/manifest_brcm_rpi.xml -L https://raw.githubusercontent.com/futgit2030/android_kernel_manifest/a17_avb/manifest_brcm_rpi.xml --create-dirs
```

3. Sync source code:

```
repo sync
```

4. Compile:

Raspberry Pi 5:
```
# defconfig neu erzeugen
tools/bazel build --config=fast //common:rpi5_defconfig

# update defconfig
cp bazel-bin/common/rpi5_defconfig/.config \
   common/arch/arm64/configs/android_rpi5_defconfig

#check:
cd common

## anzeigen, ob geändert wurde:
git diff --stat arch/arm64/configs/android_rpi5_defconfig

## anzeige der geänderten Parameter:
git diff arch/arm64/configs/android_rpi5_defconfig | grep '^+CONFIG'

## nochmals check der neuen kernel settings:
grep -E "NET_SCH_(CBS|ETF|TAPRIO|MQPRIO|PRIO|MULTIQ)|VLAN_8021Q|BROADCOM_PHY|IKCONFIG_PROC"   arch/arm64/configs/android_rpi5_defconfig

#compile
tools/bazel build --config=fast --config=stamp //common:rpi5
```

Compiled kernel Image, dtbs, and overlays can be found in `bazel-bin/common/rpi5/arch/arm64/boot` directory.

Replace existing files in `device/brcm/rpi5-kernel` directory of the Android source tree to include them in Android 17 build. You can also replace existing files in the boot partition Raspberry Pi 5 Android 17 image.
