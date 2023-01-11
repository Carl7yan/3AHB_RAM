# ---------------------------
# User variables
# ---------------------------
TB       	= rkv_ahbram_tb
SEED     	= 1
GUI      ?= 0
COV      ?= 0
DOTCL    ?= 1
VERB     ?= UVM_HIGH
OUT      ?= out
TESTNAME ?= rkv_ahbram_reset_w2r_test
DFILES  	= ../../verilog/ahb_blockram_32.v

VFILES   +=  ../vip_lib/lvc_ahb/lvc_ahb_pkg.sv \
						 ../vip_lib/lvc_ahb/lvc_ahb_if.sv \
						 ../env/rkv_ahbram_pkg.sv \
						 ../tb/rkv_ahbram_if.sv \
						 ../tb/rkv_ahbram_tb.sv


# ---------------------------
# Environment variables
# ---------------------------
inc_dir   = +incdir+../../verilog \
						+incdir+../vip_lib/lvc_ahb/{.,sequence_lib} \
						+incdir+../{cfg,cov,reg,env,seq_lib,seq_lib/elem_seqs,test}

COMP     = vcs \
					 -cpp g++-4.8 -cc gcc-4.8 -LDFLAGS -Wl,--no-as-needed -lpthread \
					 -full64 -sverilog -ntb_opts -timescale=1ps/1psuvm-1.2 -debug_acc+all -nc -l $(OUT)/log/comp.log -sim_res=1ps $(inc_dir)
RUN      = $(OUT)/obj/$(TB).simv -l run.log -sml +ntb_random_seed=$(SEED) +UVM_TESTNAME=$(TESTNAME) +UVM_VERBOSITY=$(VERB) -cm_dir $(CM_DIR) -cm_name $(CM_NAME)

COV_OPTS = -full64 -dir $(CM_DIR)
CM_DIR  ?= $(OUT)/cov.vdb
CM_NAME ?= $(TESTNAME)_$(SEED)
SIMRUNFILE 	= rkv_ahbram_sim_run.do

ifeq ($(GUI),1)
RUN += -gui
endif

ifeq ($(DOTCL),1)
RUN += -ucli -do $(SIMRUNFILE)
endif

ifeq ($(COV),1)
	COMP  += -cm line+cond+fsm+tgl+branch+assert -cm_dir $(CM_DIR)
	RUN += -cm line+cond+fsm+tgl+branch+assert -covg_cont_on_error
endif


comp:
	mkdir -p $(OUT)/work
	mkdir -p $(OUT)/log
	mkdir -p $(OUT)/sim
	mkdir -p $(OUT)/obj
	$(COMP) -top $(TB) -o $(OUT)/obj/$(TB).simv $(DFILES) $(VFILES)

run:
	$(RUN)

vcd:
	mkdir -p $(OUT)/work
	mkdir -p $(OUT)/log
	mkdir -p $(OUT)/sim
	mkdir -p $(OUT)/obj
	$(COMP) +fsdb+autoflush -l run.log -top $(TB) -o $(OUT)/obj/$(TB).simv $(DFILES) $(VFILES)

vd:
	verdi +v2k -sv \
	+incdir+@{UVM_HOME}/src ${UVM_HOME}/src/uvm.sv  \
	$(inc_dir) \
 	$(DFILES) $(VFILES) -ssf *.fsdb -nologo &





mergecov:
	urg -format both $(COV_OPTS)

dvecov:
	dve $(COV_OPTS)

verdicov:
	verdi -cov -covdir $(CM_DIR)

htmlcov:
	firefox urgReport/dashboard.html

clean:
	rm -rf $(OUT) 64 AN.DB DVEfiles csrc *.simv *.simv.daidir *.simv.vdb ucli.key
	rm -rf *.log* *.vpd *.h urgReport

