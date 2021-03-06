###########################
## FPGA Project Makefile ##
###########################

all: help

## Global Design Variables ##
DESIGN_PATH 	= $(PWD)
SOURCE_PATH 	= $(DESIGN_PATH)/src
SIM_PATH 	= $(DESIGN_PATH)/sim
SYN_PATH  	= $(DESIGN_PATH)/syn
SCRIPTS_PATH 	= $(DESIGN_PATH)/scripts
SIMTOP 		= tb_ram
TOP 		= ram
FAMILY 		= "Cyclone V"
NUM_PROCESSORS  = 2

## Generate list of source files ##
pref 		= --source=
files 		= $(wildcard $(SOURCE_PATH)/*.v)
SOURCE_FILES 	= $(foreach file, $(files), $(pref)$(file))
export SOURCE_FILES

.PHONY: syn clean

help:
	@echo " Synthesize and simulate Verilog/VHDL with Quartus and ModelSim "
	@echo " ============================================================== "
	@echo
	@echo " make syn 		: Synthesize the currently defined design"
	@echo
	@echo " make sim 		: Simulate the design using ModelSim command line"
	@echo 
	@echo " make sim-gui		: Simulate the design using ModelSim's GUI"
	@echo
	@echo " make clean 		: Cleans everything Quartus related"
	@echo

sim-compile:
	mkdir -p $(SIM_PATH)
	cd $(SIM_PATH)
	vlib $(SIM_PATH)/WORK
	vmap work $(SIM_PATH)/WORK
	for each in $(wildcard $(SOURCE_PATH)/*.vhd) ; do \
                vcom -quiet $$each ; \
        done
	for each in $(wildcard $(SOURCE_PATH)/*.v) ; do \
		vlog -quiet $$each ; \
	done

sim-gui: sim-compile
	vsim $(SIMTOP)

sim: sim-compile
	vsim -c -do "set t [clock milliseconds]; run -a; set t2 [clock milliseconds];" $(SIMTOP) | tee sim.out

syn:
	mkdir -p $(SYN_PATH)
	cp template.sdc $(SYN_PATH)/$(TOP).sdc
	BUILD_PATH=$(SYN_PATH) TOP=$(TOP) NUM_PROCESSORS=$(NUM_PROCESSORS) FAMILY=$(FAMILY) quartus_sh -t $(SCRIPTS_PATH)/q_syn.tcl

clean:
	@echo "rm *~"
	-@rm *~ 2>/dev/null || true
	@echo "rm modelsim.ini"
	-@rm modelsim.ini 2>/dev/null || true
	@echo "rm transcript"
	-@rm transcript 2>/dev/null || true
	@echo "rm $(SOURCE_PATH)/*~"
	-@rm $(SOURCE_PATH)/*~ 2>/dev/null || true
	@echo "rm -rf $(SYN_PATH)"
	-@rm -rf $(SYN_PATH) 2>/dev/null || true
	@echo "rm -rf $(SIM_PATH)"
	-@rm -rf $(SIM_PATH) 2>/dev/null || true
	@echo "rm *.out"
	-@rm *.out 2>/dev/null || true

