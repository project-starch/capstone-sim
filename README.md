# The Capstone-RISC-V Spike Simulator SDK

The Capstone-RISC-V Spike Simulator SDK is based on the [RSS SDK](https://github.com/riscv-zju/riscv-rss-sdk).
The simulator simulates a RISC-V processor with the Capstone extension.
A Linux kernel is loaded in the normal world and the simulator is started.
The simulation of Capstone extension follows the [Capstone-RISC-V ISA](https://capstone.kisp-lab.org/specs/) and can be used in the secure world.

The functions of each folder in the project are as follows:

|       Folder        |      Description       |   Version   |
| :-----------------: | :--------------------: | :---------: |
|    repo/buildroot    |    Build Initramfs     |  2021.2.x  |
|      repo/linux      |      Linux Kernel      |    5.12.0    |
| repo/riscv-gnu-toolchain | GNU Compiler Toolchain |  gcc 10.2.0 ld 2.36  |
| repo/riscv-(isa-sim,pk)  | Simulator & Bootloader |    master   |
| repo/opensbi  | Supervisor / Bootloader |    master   |
|         conf        |     Config for SDK     |             |

## Quick Start

The easiest way to get started is to use the [Apptainer](https://apptainer.org/) image defined in the `container` folder.
This will build the toolchain and the [Spike](https://github.com/project-starch/transcapstone-spike) simulator,
and then run the simulator with Capstone-RISC-V extension.
Make sure you have Apptainer already installed and simply run `make` inside the `container` folder.

```bash
git clone https://github.com/project-starch/transcapstone-sim.git
cd transcapstone-sim/container
make
```
If you have your own Apptainer image and want to prevent `make` from building one, you can set `EXTERNAL_CONTAINER_IMG`:

```
make EXTERNAL_CONTAINER_IMG=<path-to-your-image>
```

If you want to change the default size of the normal memory and the secure world, you can set `SPIKE_NORMAL_MEM` and `SPIKE_SECURE_MEM`:

```bash
make SPIKE_NORMAL_MEM=0x80000000:0x80000000 SPIKE_SECURE_MEM=0x100000000:0x80000000
```

## Build the SDK

Build dependencies on Ubuntu 16.04/18.04/20.04:

> **Note: Do not set `$RISCV` and make sure no origin RISC-V toolchain in your `$PATH`.**

```bash
sudo apt install device-tree-compiler autoconf automake autotools-dev \
curl libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo \
gperf libtool patchutils bc zlib1g-dev libexpat-dev python-dev python3-dev unzip \
libglib2.0-dev libpixman-1-dev git rsync wget cpio
```

```bash
git clone https://github.com/project-starch/transcapstone-sim.git
cd transcapstone-sim
git submodule update --init --recursive --progress
make sim-transcapstone
```

## Specify Spike Parameters

### Specify the number of harts

The default is 1. To override this number,
set the `SPIKE_NCORES` variable for `make`.
For example:

```
make sim-transcapstone SPIKE_NCORES=4
```

### Specify the secure-world memory region

The default is `0x100000000:0x80000000`. To override this number,
set the `SPIKE_SECURE_MEM` variable for `make`.
For example:

```
make sim-transcapstone SPIKE_SECURE_MEM=0x100000000:0x40000000
```

### Use command line directly

If you want to run Spike using its interface in CLI, you can make changes to the following command:

```bash
make
```

```bash
./toolchain/bin/spike --isa=rv64imafdc -p1 -m0x80000000:0x80000000 -M0x100000000:0x80000000 -D --kernel ./build/linux/arch/riscv/boot/Image ./build/opensbi/platform/generic/firmware/fw_jump.elf
```

If you want to run Spike using proxy kernel, you can make changes to the following command:

```bash
make spike-pk
```

```bash
./toolchain/bin/spike --isa=rv64imafdc -p1 -m0x80000000:0x80000000 -M0x100000000:0x80000000 -D ./build/riscv-pk/pk [path-to-your-program]
```

## Debugging

To enable debugging instructions in Spike, use

```
make debug-transcapstone
```

To debug Spike using GDB with debugging instructions, use 

```
make gdb-transcapstone
```

To check memory leak of Spike using Valgrind with debugging instructions, use 

```
make valgrind-transcapstone
```

