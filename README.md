# 鸣谢 Thanks
* [HandsomeMod](https://github.com/HandsomeMod/HandsomeMod)
* [dreirund/Handsomemod-uf896](https://github.com/dreirund/Handsomemod-uf896)
* [kirdesde/Handsomemod](https://github.com/kirdesde/Handsomemod/)
* [ALbur/HanFI](https://github.com/ALbur/HanFI)

# 本项目 HandsomeMod-uf896-ci
- **用法**
  1. Action构建出boot和system（rootfs）
  2. 替换别的棒子底包的boot.img和rootfs.img
  3. 恢复基带，修复Modem

- **兼容性**
  1. uf896的构建制品同样适用于 uf629 v1.1
  2. 短接【1，6】测试点进入9008模式

- **参考信息**
  1.  [openstick#edl-recovery-mode](https://github.com/colemickens/mobile-nixos/tree/openstick/devices/openstick#edl-recovery-mode)
  2.  [UF896](https://forum.openwrt.org/t/uf896-qualcomm-msm8916-lte-router-384mib-ram-2-4gib-flash-android-openwrt/131712/59)

- **Modem**
  1. 提取基带: modemst1、modemst1、fsc、fsg 四个分区,并替换
  2. 可以用miko备份的文件包xxx.bin文件里面的 0.modem.fat （可以用7z压缩工具打开提取）
  3. 在提取 0.modem.fat （可以用7z压缩工具打开提取）到一个文件夹
  4. 把image下面的所有文件复制到 /lib/firmware/，reboot重启棒子

# 援引 HanFI
* 稳妥起见 刷机前请备份自己的基带4分区及firmware
* 也许你需要自行替换firmware及基带 详见 [大佬文章](https://www.coolapk.com/feed/38895153) 已验证
* tools都不带系统镜像 其中tool.7z已验证 其余需要自行验证 ps:其实刷了大佬们的op后 替换自己编译的 root和boot就行 用高通工具9008更快
* 需要将action生成的OpenWrt_firmware_Handsome_handsome-openstick-ufi001c_xxxx.zip 中的两个img放入你需要的tool解压后目录
* 重命名.bat=>双击.bat/刷入.bat=>刷入 
* 刷入前请自行删除旧版本boot.img及rootfs.img(tool.7z下为system.img)
* firmware 见 [openstick](https://github.com/OpenStick/OpenStick/releases)
* 问题 
  1. mac80211驱动有问题，要编译老网卡需要换，自己添加wcn36xx驱动进去 还望大佬们指导编译方案 也不清楚是否可行
  2. kmod-usb-gadget-eth 编译不进去 使用扩展坞的 若有网卡 或许需要本地编译 或者请指导一下云编译怎么修复
* 其他
  1. 打开主机模式，连接拓展坞 echo host > /sys/kernel/debug/usb/ci_hdrc.0/role
  2. 打开网络共享，关闭主机模式 echo gadget > /sys/kernel/debug/usb/ci_hdrc.0/role

# 援引 Handsomemod-uf896

This repository is a fork of the ["`uf896`"-branch](https://github.com/kirdesde/Handsomemod/tree/uf896) of "[kirdesde/Handsomemod](https://github.com/kirdesde/Handsomemod/)", which itself is based on "[HandsomeMod/HandsomeMod](https://github.com/HandsomeMod/HandsomeMod/)".

It is an operating system based on [OpenWrt](http://openwrt.org/), but with many changes and additions.

As "[kirdesde/Handsomemod](https://github.com/kirdesde/Handsomemod/)", this repository too is especially targeted at supporting the UF896 LTE/WiFi routers which come in the form factor of an USB stick. A discussion about this devices with links to further projects can be found [here on the OpenWrt forum](https://forum.openwrt.org/t/131712).

The purpose of this repository is to have a place for my modifications and fixes I apply for the UF896 support.

It is based on [OpenWrt 21.02](https://openwrt.org/releases/21.02/) and heavily modified. It uses a [Linux kernel](https://kernel.org/) version 5.10.33.


## Building

In general, the [building instructions from the OpenWrt project](https://openwrt.org/docs/guide-developer/toolchain/use-buildsystem) apply, but different repositories are used.

To build the firmware for the UF896 from this repository, use the following steps:

1. Make sure you have the [OpenWrt build requirements](https://openwrt.org/docs/guide-developer/toolchain/install-buildsystem) fulfilled.  
   *On [Arch Linux](http://archlinux.org) and Arch based distributions, installing the [AUR package "`openwrt-devel`"](https://aur.archlinux.org/packages/openwrt-devel) does pull in all needed dependencies.*
2. Clone this repository: `git clone https://github.com/dreirund/Handsomemod-uf896.git`.
3. `cd` into the clone: `cd Handsomemod-uf896`.
4. Generate `feeds.conf` from the default: `cp feeds.conf.default feeds.conf`.
5. Make sure that in the `feeds.conf` the following feeds are enabled *(note that the "`packages`" feed comes from HandsomeMod and* not *from OpenWrt, and also note the extra "`handsomefeed`" feed)*:  
   ```
   src-git packages https://github.com/HandsomeMod/packages.git
   src-git luci https://git.openwrt.org/project/luci.git;openwrt-21.02
   src-git routing https://git.openwrt.org/feed/routing.git;openwrt-21.02
   src-git telephony https://git.openwrt.org/feed/telephony.git;openwrt-21.02
   src-git handsomefeed https://github.com/HandsomeMod/handsomefeed.git
   ```  
   *(Other feeds might be available as well.)*
6. Install packages from the feeds: `./scripts/feeds update -a && ./scripts/feeds install -a`.
7. Select the default configuration for the `UF896`:  
   1. `make menuconfig`,
   2. Select the following options:
      1. `Target System`: `Qualcomm Msm89xx Soc` (`CONFIG_TARGET_msm89xx=y`),
      2. `Subtarget`: `Msm8916 Soc based Targets` (`CONFIG_TARGET_msm89xx_msm8916=y`),
      3. `Target Profile`: `Openstick UF896` (`CONFIG_TARGET_msm89xx_msm8916_DEVICE_openstick_uf896=y`),
      4. Select `Save` and then `Exit` the configuration (*do not* do further configurations now).
   3. Generate default configuration for the selected target: `make defconfig`.
8. If you want, configure options to your liking now: `make menuconfig`.
9. Build the firmware image: `make`.

You can also use [example `.config`](sample/.config) (that is my `.config` I use on the device) and [example `feeds.conf`](sample/feeds.conf) from the [`sample/`](sample/) directory.

For more build options and troubleshooting, refer to the [OpenWrt build system usage](https://openwrt.org/docs/guide-developer/toolchain/use-buildsystem) and other [documentation at OpenWrt.org](https://openwrt.org/docs/).


## Installing

*(Instructions taken from [&rarr;&hairsp;here](https://github.com/kirdesde/Handsomemod/commit/aec867af57f1212a5ec3c4dea2828463bc31ea29). Not guaranteed to work -- I myself am currently stuck at `fastboot erase rootfs` because my device does not has a partition with that name, see [&rarr;&hairsp;here](https://forum.openwrt.org/t/131712/92).)*

Carry out the following from a computer where the device is connected to via USB.

### Prerequisites:

***Have [OpenStick](https://github.com/OpenStick/OpenStick) installed (or at least the partitions prepared for it)**, the images generate here assume that the device has been prepared for OpenStick operating system before!*

*More detailed instructions on this step hopefully soon.*

### Pre-cautions work:

1. Before conneting the device, make sure that there is currently no other device in fastboot mode connected, otherwise you can damage the other device:  
   `fastboot devices`  
   should return nothing.
2. Connect the device, and check that  
   `fastboot devices`  
   now returns something which looks similar to  
   ```
   0ec38c91 fastboot
   ```  
   (The ID can vary).

If that is given, proceed:

### Install instructions:

1. Erase boot partition:  
   `fastboot erase boot`
2. Write boot image:  
   `fastboot flash boot handsomemod-msm89xx-msm8916-openstick_uf896-squashfs-boot.img`
3. Erase rootfs partition:  
   `fastboot erase rootfs`
4. Write system image:  
   `fastboot flash rootfs handsomemod-msm89xx-msm8916-openstick_uf896-squashfs-system.img`
5. Reboot:  
   `fastboot reboot`
6. "OpenWrt" (HandsomeMod) should boot now (red led is blinking).
7. push the sysupgrade image to the device (this is imported, as this sets up the loop device for the rootfs overlay)  
   *(**Note:** `adbd` is part of the handsome feeds utils package, **Make sure you have `CONFIG_PACKAGE_android-tools-adbd=y`!**)*  
   `adb push handsomemod-msm89xx-msm8916-openstick_uf896-squashfs-sysupgrade.bin /tmp`
8. Execute sysupgrade:  
   `adb shell`  
   `sysupgrade /tmp/handsomemod-msm89xx-msm8916-openstick_uf896-squashfs-sysupgrade.bin`
9. After reboot you should have a working openwrt incl. overlay fs
10. Thanks to the usb gadget mode the device has ethernet connectivity over usb
11. Please note, it's also part of the handsome feeds utils to setup and activate a default wifi AP (unencrypted). You can disable this after the last reboot (or disable it in the build config)
