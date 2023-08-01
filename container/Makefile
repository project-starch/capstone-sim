SPIKE = ./toolchain/bin/spike
LINUX_IMAGE = ./build/linux/arch/riscv/boot/Image 
FW_JUMP = ./build/opensbi/platform/generic/firmware/fw_jump.elf
ISA ?= rv64imafdc
SPIKE_NCORES ?= 1
SPIKE_NORMAL_MEM ?= 0x80000000:0x80000000
SPIKE_SECURE_MEM ?= 0x100000000:0x80000000

ifndef EXTERNAL_CONTAINER_IMG
CONTAINER_DEF=dev.def
CONTAINER_IMG=dev.sif

$(CONTAINER_IMG): $(CONTAINER_DEF)
	apptainer build -F $@ $<
else
CONTAINER_IMG=$(EXTERNAL_CONTAINER_IMG)
endif

all: $(CONTAINER_IMG)
	$(SPIKE) --isa=$(ISA) -p$(SPIKE_NCORES) -m${SPIKE_NORMAL_MEM} -M${SPIKE_SECURE_MEM} --kernel $(LINUX_IMAGE) $(FW_JUMP)

clean:
	rm -f $(CONTAINER_IMG)

.PHONY: all clean
