# [Xenomai](https://xenomai.org/)-patch ARTIK710 Linux kernel

## Initialize repositories

- `git clone --recursive https://github.com/eunchurn/apros-artik710`

or

- Run `git clone` and then `git submodule update --init --recursive`

## Install dependencies packages

`sudo apt-get install kpartx u-boot-tools gcc-arm-linux-gnueabihf gcc-aarch64-linux-gnu device-tree-compiler android-tools-fsutils curl`

## Xenomai Kernel Patch

`linux-artik`'s `A710/v4.1` branch is Linux kernel v4.1.15. So patch first from v4.1.15 to v4.1.18 with `kernel-patch-15-18.patch`. And then [Xenomai](https://xenomai.org/) patch with `ipipe-core`

```bash
cd linux-artik && git checkout A710/v4.1
cat ../kernel-patch-15-18.patch | patch -p1
../xenomai/scripts/prepare-kernel.sh --arch=arm64 --ipipe=../ipipe-core-4.1.18-arm64-8.patch
```

## Configuration kernel

```
make ARCH=arm64 menuconfig
```

- General setup -> Timers subsystem
  - [x] High Resolution Timer Support (Enable)
- Xenomai/cobalt -> Drivers -> RTnet
  - [x] RTnet, TCP/IP socket interface (Enable)
  - Drivers
    - [x] New intel(R) PRO/1000 PCIe (Enable)
    - [x] Realtek 8169 (Enable)
    - [x] Loopback (Enable)
  - Add-ons
    - [x] Real-Time Capturing Support (Enable)
- CPU Power Management
  - [ ] CPU Ide (disable)
  - [ ] CPU Power Management -> CPU Frequency scaling (disable)

- Device Drivers
  - [ ] CONFIG_INPUT_PCSPKR : Input device support -> Miscellaneous devices -> PC Speaker support (disable)

## Build kernel

```
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- Image -j4
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- dtbs
mkdir usr/modules
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- modules_install INSTALL_MOD_PATH=usr/modules INSTALL_MOD_STRIP=1
make_ext4fs -b 4096 -L modules \
	-l 32M usr/modules.img \
	usr/modules/lib/modules/
rm -rf usr/modules
```

## Update board

Copy compiled binaries into your board.

```
scp arch/arm64/boot/Image root@{YOUR_BOARD_IP}:/root
scp arch/arm64/boot/dts/nexell/*.dtb root@{YOUR_BOARD_IP}:/root
scp usr/modules.img root@{YOUR_BOARD_IP}:/root
```

On your board

```
mount -o remount,rw /boot
cp /root/Image /boot
cp /root/*.dtb /boot
dd if=/root/modules.img of=/dev/mmcblk0p2
sync
reboot
```

## Failed

```
Starting kernel ...

[    0.750000] cw201x 8-0062: ret=0 version(6f)
[    0.750000] cw201x 8-0062: rkbat config 2
[    0.940000] [Xenomai] init failed, code -19
[    0.970000] /soc/display_drm_mipi: could not find node 'display-timing'
[    2.170000] atmel_mxt_ts 2-004a: __mxt_read_reg: i2c transfer failed (-6)
```