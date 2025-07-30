# About this repo

This repo (`android14-lineage22-mod` branch) is based on [Lineage OS 22 xiaomi sm8250 kernel source](https://github.com/LineageOS/android_kernel_xiaomi_sm8250).

Originally this repo (`android12-stable-mod` or `android14-stable-mod` branch) is fork from [UtsavBalar1231's repo](https://github.com/UtsavBalar1231/kernel_xiaomi_sm8250), but the `android14-stable` branch has freeze problem when the device sleep for a while or wake up. So now I switched to lineage22 codebase. The MIUI features code and some parts of the drivers is still copied from UtsavBalar1231's branch.

So still Thanks to [@UtsavBalar1231](https://github.com/UtsavBalar1231/)!

(The devices affected by the "1% battery bug" are: alioth, apollo, lmi, thyme, umi, pipa. Because they all use the PM8150, aka Qualcomm fuel gauge GEN4. For the other devices are not affected by that bug, you can use this kernel for KernelSU purpose, as a replacement of the orginal stock kernel. Also, as the people tested, this kernel NoKernelSU version is good for applying [APatch](https://github.com/bmax121/APatch)).

The pre-built kernel image/zip is built on the `android14-lineage22-mod` branch. It should works on both stock MIUI and third-party AOSP based Android 11-14 ROMs. You are welcomed to give feedback (Issues/Pull Requests)!

Note: The zip does not include the `dtbo.img` and it will not replace your `dtbo` partition. It is recommanded to keep the stock `dtbo` or the `dtbo` from your third-party rom (If the builder comfirmed it works well). Since there are some problems with the `dtbo.img` which built from this source, one of them is the screen will suddently goes to the highest brightness when shut try to shut off the screen in the lock screen. If you had flashed any other third-party kernels, and you got some weird problem, you should keep an eye to check your `dtbo` has been replaced or not. 

Supported devices:
| Code Name | Device Name                          |
|-----------|--------------------------------------|
| psyche    | Xiaomi Mi 12X                        |
| thyme     | Xiaomi Mi 10S                        |
| umi       | Xiaomi Mi 10                         |
| munch     | Poco F4 / Redmi K40S                 |
| lmi       | Redmi K30 Pro                        |
| cmi       | Xiaomi Mi 10 Pro                     |
| cas       | Xiaomi Mi 10 Ultra                   |
| apollo    | Xiaomi Mi 10T / Redmi K30S Ultra     |
| alioth    | Xiaomi Mi 11X / POCO F3 / Redmi K40  |
| elish     | Xiaomi Pad 5 Pro                     |
| enuma     | Xiaomi Pad 5 Pro 5G                  |
| dagu      | Xiaomi Pad 5 Pro 12.4                |
| pipa      | Xiaomi Pad 6                         |

Other Features/Improvement of this Kernel:
1. Support USB Serial (CH340/FTDI/PL2303/OTI6858/TI/SPCP8X5/QT2/UPD78F0730/CP210X).
2. Support EROFS.
3. F2FS realtime discard enabled for better TRIM the flash.
4. Support CANBus and USB CAN adapter (like CANable).
5. Support LZ4KD, LZ4, LZ4HC, ZSTD compression algorithms for ZRAM.

# Setting Up the Environment
### Prepair the basic build environment.

You have to have the basic common toolchains, such as `git`, `make`, `curl`, `bison`, `flex`, `zip`, etc, and some other packages.
In Debian/Ubuntu, you can
```bash
sudo apt install build-essential git curl wget bison flex zip bc cpio libssl-dev ccache
```
And also, you have to have `python` (only `python3` is not enough). you can install the apt package `python-is-python3`.

In RHEL/RPM based OS, you can
```bash
sudo yum groupinstall 'Development Tools'
sudo yum install wget bc openssl-devel ccache
```

Notice: `ccache` is enabled in `build.sh` for speed up the compiling. `CCACHE_DIR` has been set as `$HOME/.cache/ccache_mikernel` in `build.sh`. If you don't like you can remove or modify it.

### Download compiler toolchain

You have to have `aarch64-linux-gnu`, `arm-linux-gnueabi`, `clang`. [Proton Clang](https://github.com/kdrag0n/proton-clang/) is a good prebuilt clang cross compiler toolchain.

The default toolchain path is `$HOME/proton-clang/proton-clang-20210522/bin` which is set in `build.sh`. If you are using another location please change `TOOLCHAIN_PATH` in `build.sh`.

```bash
mkdir proton-clang
cd proton-clang
wget https://github.com/kdrag0n/proton-clang/archive/refs/tags/20210522.zip
unzip 20210522.zip
cd ..
```

# Build Kernel

> [!NOTE]
> If you see errors like `.relr.dyn` or `cannot find libc.so.6`, your toolchain's `ld` is too old. Remove it to use the system linker, or update your toolchain.
    
* **KSU_VERSION** (Select KernelSU version):

  * `ksu`: [Official KernelSU (v0.9.5)](https://github.com/tiann/KernelSU/tree/v0.9.5)
  * `rksu`: [RKSU](https://github.com/rsuntk/KernelSU)
  * `sukisu`: [SukiSU](https://github.com/ShirkNeko/KernelSU)
  * `sukisu-ultra`: [SukiSU-Ultra](https://github.com/SukiSU-Ultra/SukiSU-Ultra)

* **ADDITIONAL_FUNCTION** (Enable `SuSFS` or `KPM`):

  * `susfs`: Enable [SuSFS](https://gitlab.com/simonpunk/susfs4ksu)
  * `kpm`: Enable [KPM](https://github.com/SukiSU-Ultra/SukiSU_KernelPatch_patch)
  * `susfs-kpm` Enable SuSFS and KPM
  * Any other value: Disable `SuSFS` and `KPM`

* **SYSTEM** (Target system type):

  * `miui`
  * `aosp`
  * Any other value: Build for all supported systems

**To build the kernel, run:**
    
```bash
bash build.sh TARGET_DEVICE [KSU_VERSION] [ADDITIONAL_FUNCTION] [SYSTEM]
```
