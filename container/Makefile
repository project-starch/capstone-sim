ifndef EXTERNAL_CONTAINER_IMG
CONTAINER_DEF=capstone_sim.def
CONTAINER_IMG=capstone_sim.sif

$(CONTAINER_IMG): $(CONTAINER_DEF)
	apptainer build -F $@ $<
else
CONTAINER_IMG=$(EXTERNAL_CONTAINER_IMG)
endif

all: $(CONTAINER_IMG)

clean:
	rm -f $(CONTAINER_IMG)

.PHONY: all clean
