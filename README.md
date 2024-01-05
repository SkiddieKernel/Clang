# SKiddie Clang

This is a [LLVM](https://llvm.org/) and [Clang](https://clang.llvm.org/) compiler toolchain built for kernel development. Builds are always made from the latest LLVM sources rather than stable releases, so complete stability cannot be guaranteed.
This toolchain targets the AArch32, AArch64, and x86 architectures. It is built with LTO and PGO to reduce compile times as much as possible. [Polly](https://polly.llvm.org/), LLVM\'s polyhedral loop optimizer, is also included for users who want to experiment with additional optimization. Note that this toolchain is **not** suitable for anything other than bare-metal development; it has not been built with support for any libc or userspace development in mind.
## Host compatibility

This toolchain is built on Debian GNU/Linux 12 (bookworm), which uses glibc 2.36. Compatibility with older distributions cannot be guaranteed. Other libc implementations (such as musl) are not supported.

## Download latest built

* <a href=https://github.com/SkiddieKernel/Clang/releases/download/202401060333/skiddie-clang-18.0.0-7c3bcc3-202401060333.tar.zst>skiddie-clang-18.0.0-7c3bcc3-202401060333.tar.zst</a>
## Building Linux Kernel

This is how you start initializing the SKiddie Clang to your server, use a command like this:

```bash
# Create a directory for the source files
mkdir -p ~/toolchains/skiddie-clang
```

Then download latest built:

```bash
wget -c https://github.com/SkiddieKernel/Clang/releases/download/202401060333/skiddie-clang-18.0.0-7c3bcc3-202401060333.tar.zst -O - | tar --use-compress-program=unzstd -xf - -C ~/toolchains/skiddie-clang

```

Make sure you have this toolchain in your `PATH`:

```bash
export PATH="~/toolchains/skiddie-clang/bin:$PATH"

```

For an `AArch64` cross-compilation setup, you must set the following variables. Some of them can be environment variables, but some must be passed directly to `make` as a command-line argument. It is recommended to pass **all** of them as `make` arguments to avoid confusing errors:

- `CC=clang` (must be passed directly to `make`)
- `CROSS_COMPILE=aarch64-linux-gnu-`
- If your kernel has a 32-bit vDSO: `CROSS_COMPILE_ARM32=arm-linux-gnueabi-`

Optionally, you can also choose to use as many LLVM tools as possible to reduce reliance on binutils. All of these must be passed directly to `make`:

- `AR=llvm-ar`
- `NM=llvm-nm`
- `OBJCOPY=llvm-objcopy`
- `OBJDUMP=llvm-objdump`
- `STRIP=llvm-strip`

Android kernels older than 4.14 will require patches for compiling with any Clang toolchain to work; those patches are out of the scope of this project. See [android-kernel-clang](https://github.com/nathanchance/android-kernel-clang) for more information.

Android kernels 4.19 and newer use the upstream variable `CROSS_COMPILE_COMPAT`. When building these kernels, replace `CROSS_COMPILE_ARM32` in your commands and scripts with `CROSS_COMPILE_COMPAT`.

### Differences from other toolchains

SKiddie Clang has been designed to be easy-to-use compared to other toolchains, such as [AOSP Clang](https://android.googlesource.com/platform/prebuilts/clang/host/linux-x86/). The differences are as follows:

- `CLANG_TRIPLE` does not need to be set because we don\'t use AOSP binutils.
- `LD_LIBRARY_PATH` does not need to be set because we set library load paths in the toolchain.
- No separate GCC/binutils toolchains are necessary; all tools are bundled.
