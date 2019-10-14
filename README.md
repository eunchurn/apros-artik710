# Xenomai-patch ARTIK710 Ubuntu OS

## Initialize repositories

- `git clone --recursive https://github.com/eunchurn/apros-artik710`

or

- Run `git clone` and then `git submodule update --init --recursive`

## Install dependencies packages

`sudo apt-get install kpartx u-boot-tools gcc-arm-linux-gnueabihf gcc-aarch64-linux-gnu device-tree-compiler android-tools-fsutils curl`

## Xenomai Kernel Patch

```bash
cd linux-artik && git checkout A710/v4.1
cat ../kernel-patch-15-18.patch | patch -p1
../xenomai/scripts/prepare-kernel.sh --arch=arm64 --ipipe=../ipipe-core-4.1.18-arm64-8.patch
```

copy `.config` file to `linux-artik`

or

`sudo make menuconfig`

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
- Power Management and ACPI options
  - [ ] CONFIG_CPU_FREQ : CPU Frequency scaling -> disable(disable)
  - [ ] CONFIG_ACPI_PROCESSOR : ACPI Support -> Processor(disable)
  - [ ] CONFIG_CPU_IDLE : CPU Idle -> CPU idle PM Support(disable)
- Device Drivers
  - [ ] CONFIG_INPUT_PCSPKR : Input device support -> Miscellaneous devices -> PC Speaker support (disable)


