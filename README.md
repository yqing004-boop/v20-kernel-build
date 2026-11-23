# HONOR V20 (PCT-AL10) kernel build

Compile Huawei's official GPL kernel source via GitHub Actions, extract `vmlinux` symbols for GhostLock (CVE-2026-43499) research.

## Target device

| item | value |
|---|---|
| model | HONOR V20 (PCT-AL10) |
| codename | Princeton |
| SoC | Kirin 980 |
| OS | HarmonyOS 4.0.0 (4.0.0.121) |
| kernel | Linux 4.14.116, built 2024-01-24 |
| security patch | 2024-02-01 |

## Why official GPL source

Huawei publishes kernel source under GPL. For Kirin 980, HOS 3.0 and HOS 4.0 share the same kernel baseline (4.14.116), so symbols from the HOS3 source package match the device.

## Usage

1. Actions -> Build V20 Kernel -> Run workflow
2. Wait ~40-90 min
3. Download `v20-kernel-artifacts`

Artifacts:
- `vmlinux` - full kernel image with symbols
- `System.map` - symbol address table
- `kernel.config` - config actually used
- `build.log` - full build log

## Local build

```bash
sudo apt install -y build-essential bc bison flex libssl-dev libelf-dev \
  cpio rsync kmod device-tree-compiler gcc-aarch64-linux-gnu binutils-aarch64-linux-gnu

curl -L -o pct.tar.gz 'https://download-c1.huawei.com/download/downloadCenter?downloadId=1D5E58A281FBDBBE7A520B08B8C9A784&version=36E53E6A525178CF28BDC47B6FBF3CAA&siteCode=worldwide'
tar -xzf pct.tar.gz

curl -L -o clang.tar.gz 'https://android.googlesource.com/platform/prebuilts/clang/host/linux-x86/+archive/refs/heads/android10-release/clang-r353983c.tar.gz'
mkdir clang && tar -xzf clang.tar.gz -C clang

cd Code_Opensource/kernel
cp ../../configs/v20_defconfig.txt .config
make ARCH=arm64 O=../../out \
  CC=$PWD/../../clang/bin/clang CLANG_TRIPLE=aarch64-linux-gnu- \
  CROSS_COMPILE=aarch64-linux-gnu- LD=ld.lld -j$(nproc)
```

## License

Kernel source is GPL-2.0 (Huawei release). Scripts in this repo are MIT.
