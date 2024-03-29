# The Capstone-RISC-V Spike Simulator SDK

The Capstone-RISC-V Spike Simulator SDK is based on the [RSS SDK](https://github.com/riscv-zju/riscv-rss-sdk).
The simulator simulates a RISC-V processor with the Capstone-RISC-V extension.
A Linux kernel is loaded in the normal world and the simulator is started.
The simulation of Capstone-RISC-V extension follows the [Capstone-RISC-V ISA](https://capstone.kisp-lab.org/specs/).

The functions of each folder in the project are as follows:

| Folder | Description | Version | Capstone Adaption/Specific |
| :----: | :---------: | :-----: | :-----------------: |
|  container  |  Apptainer image for the SDK  |  -  |  -  |
|  repo/buildroot  |  Build Initramfs  |  2021.11  |  No  |
|  repo/linux  |  Linux Kernel  |  5.16.0  |  No  |
|  repo/riscv-gnu-toolchain  |  GNU Compiler Toolchain  |  gcc 11  |  No  |
|  repo/riscv-pk  |  Bootloader  |  -  |  No  |
|  repo/riscv-isa-sim  |  Simulator  |  -  |  Yes  |
|  repo/opensbi  |  Supervisor / Bootloader  |  -  |  No  |
|  conf  |  Config for SDK  |  -  |  -  |

## Quick Start

The easiest way to get started is to use the [Apptainer](https://apptainer.org/) image defined in the `container` folder.
This will build the toolchain and the [Capstone-RISC-V Spike](https://github.com/project-starch/capstone-spike) simulator,
and then run the simulation of the Capstone-RISC-V processor.
Make sure you have Apptainer already installed and simply run `make` inside the `container` folder.

```bash
git clone https://github.com/project-starch/capstone-sim.git
git submodule update --init --recursive --progress
cd capstone-sim/container
make
```

This will build an Apptainer image (`capstone_sim.sif`) under the `container` folder.

If you have your own Apptainer image and want to prevent `make` from building one, you can set `EXTERNAL_CONTAINER_IMG`:

```
make EXTERNAL_CONTAINER_IMG=<path-to-your-image>
```

## Build the SDK

**Ubuntu 16.04/18.04/20.04:**

> **Note: Do not set `$RISCV` and make sure no origin RISC-V toolchain in your `$PATH`.**

```bash
sudo apt install device-tree-compiler autoconf automake autotools-dev \
curl libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo \
gperf libtool patchutils bc zlib1g-dev libexpat-dev python-dev python3-dev unzip \
libglib2.0-dev libpixman-1-dev git rsync wget cpio
```

```bash
git clone https://github.com/project-starch/capstone-sim.git
cd capstone-sim
git submodule update --init --recursive --progress
make
```

## Specify Spike Parameters

### Specify the number of harts

> Note: Multi-hart support is still under development for Capstone-RISC-V ISA. It's recommended to keep the default value for now.

The default is 1. To override this number, set the `SPIKE_NCORES` variable for `make`.
For example:

```bash
make SPIKE_NCORES=4
```

### Specify the secure-world memory region

The default is `0x100000000:0x80000000`. To override this number,
set the `SPIKE_SECURE_MEM` variable for `make`.
For example:

```bash
make SPIKE_SECURE_MEM=0x100000000:0x40000000
```

### Use command line directly

If you want to run Spike using its interface in CLI, you can make changes to the following command:

```bash
make fw-image
make spike
```

```bash
./toolchain/bin/spike --isa=rv64imafdc -p1 -m0x80000000:0x80000000 -M0x100000000:0x80000000 --kernel ./build/linux/arch/riscv/boot/Image ./build/opensbi/platform/generic/firmware/fw_jump.elf
```

If you want to run Spike using proxy kernel, you can make changes to the following command:

```bash
make spike
make pk
```

```bash
./toolchain/bin/spike --isa=rv64imafdc -p1 -m0x80000000:0x80000000 -M0x100000000:0x80000000 ./build/riscv-pk/pk [path-to-your-program]
```

## File System in Simulation

After building the SDK, you can find the file system in `build/buildroot_initramfs_sysroot`.
You can copy the files to this folder, update the timestamp of the folder,
and rebuild the SDK to update the file system.

## For Developers

### Rebuild the SDK

If you want to rebuild the SDK after modifying the source code of any of the submodules,
you need to update the timestamp of the submodules first and rebuild the SDK.

### Debugging

To enable debugging instructions in Spike, use

```bash
make debug-capstone
```

To debug Spike using GDB with debugging instructions, use 

```bash
make gdb-capstone
```

To check memory leak of Spike using Valgrind with debugging instructions, use 

```bash
make valgrind-capstone
```
