MKFILES_DIR=$(abspath $(dir $(lastword $(MAKEFILE_LIST))))
EDAPACK_IVERILOG_DIR=$(abspath $(MKFILES_DIR)/..)
PACKAGES_DIR=$(EDAPACK_IVERILOG_DIR)/packages
BUILD_DIR=$(EDAPACK_IVERILOG_DIR)/build

include $(EDAPACK_IVERILOG_DIR)/etc/edapack-iverilog.info

ifeq (true,$(VERBOSE))
Q=
WGET=wget
UNTARGZ=tar xvzf
else
Q=@
WGET=wget -q
UNTARGZ=tar xzf
endif

uname_o:=$(shell uname -o)

ifeq (Linux,$(uname_o))
platform=linux_x86_64
else
ifeq (GNU/Linux,$(uname_o))
platform=linux_x86_64
else
platform=unknown
endif
endif

IVERILOG_DIR=verilog-$(version)
IVERILOG_TGZ=$(IVERILOG_DIR).tar.gz
IVERILOG_URL=ftp://icarus.com/pub/eda/verilog/v10/$(IVERILOG_TGZ)

all :

edapack-iverilog : $(BUILD_DIR)/iverilog-$(platform)-$(version).tar.gz


$(BUILD_DIR)/iverilog-$(platform)-$(version).tar.gz : $(BUILD_DIR)/edapack-iverilog.d
	$(Q)cd $(BUILD_DIR)/iverilog ; tar czf $@ iverilog

$(BUILD_DIR)/edapack-iverilog.d : $(PACKAGES_DIR)/$(IVERILOG_TGZ)
	$(Q)rm -rf $(BUILD_DIR)/iverilog
	$(Q)mkdir -p $(BUILD_DIR)/iverilog
	$(Q)cd $(BUILD_DIR)/iverilog ; $(UNTARGZ) $^
	$(Q)cd $(BUILD_DIR)/iverilog/$(IVERILOG_DIR) ; \
		./configure --prefix=$(BUILD_DIR)/iverilog/iverilog
	$(Q)mkdir -p $(BUILD_DIR)/iverilog/iverilog/lib/ivl
	$(Q)mkdir -p $(BUILD_DIR)/iverilog/iverilog/lib/ivl/include
	$(Q)cd $(BUILD_DIR)/iverilog/$(IVERILOG_DIR) ; $(MAKE)
	$(Q)cd $(BUILD_DIR)/iverilog/$(IVERILOG_DIR) ; $(MAKE) install
	$(Q)touch $@

$(PACKAGES_DIR)/$(IVERILOG_TGZ) :
	$(Q)if test ! -d $(PACKAGES_DIR); then mkdir -p $(PACKAGES_DIR); fi
	$(Q)$(WGET) -O $@ $(IVERILOG_URL)



