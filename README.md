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

