MKFILES_DIR=$(abspath $(dir $(lastword $(MAKEFILE_LIST))))
PROJECT_DIR=$(abspath $(MKFILES_DIR)/..)
PACKAGES_DIR=$(PROJECT_DIR)/packages
BUILD_DIR=$(PROJECT_DIR)/build
ETC_DIR=$(PROJECT_DIR)/etc

EDAPACK_BUILD_URL=https://github.com/EDAPack/edapack-build

include $(PROJECT_DIR)/etc/package.info

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

PREFIX=$(BUILD_DIR)/iverilog/iverilog
PACKAGE=$(BUILD_DIR)/iverilog-$(platform)-$(version).tar.gz

all : $(PACKAGE)

$(PACKAGE) : \
		$(BUILD_DIR)/edapack-iverilog.d \
		$(PREFIX)/etc/package.info \
		$(PREFIX)/etc/install.py \
		$(PREFIX)/etc/modulefile
	$(Q)cd $(PREFIX) ; tar czf $@ *

$(BUILD_DIR)/edapack-iverilog.d : $(PACKAGES_DIR)/$(IVERILOG_TGZ) 
	$(Q)rm -rf $(BUILD_DIR)/iverilog
	$(Q)mkdir -p $(BUILD_DIR)/iverilog
	$(Q)cd $(BUILD_DIR)/iverilog ; $(UNTARGZ) $(PACKAGES_DIR)/$(IVERILOG_TGZ)
	$(Q)cd $(BUILD_DIR)/iverilog/$(IVERILOG_DIR) ; \
		patch -p1 < $(PROJECT_DIR)/scripts/iverilog.patch
	$(Q)cd $(BUILD_DIR)/iverilog/$(IVERILOG_DIR) ; \
		./configure --prefix=$(PREFIX) \
			CXXFLAGS="-O3" CFLAGS="-O3"
	$(Q)mkdir -p $(BUILD_DIR)/iverilog/iverilog/bin
	$(Q)mkdir -p $(BUILD_DIR)/iverilog/iverilog/lib/ivl
	$(Q)mkdir -p $(BUILD_DIR)/iverilog/iverilog/lib/ivl/include
	$(Q)mkdir -p $(BUILD_DIR)/iverilog/iverilog/include/iverilog
	$(Q)mkdir -p $(BUILD_DIR)/iverilog/iverilog/share/man/man1
	$(Q)cd $(BUILD_DIR)/iverilog/$(IVERILOG_DIR) ; $(MAKE)
	$(Q)cd $(BUILD_DIR)/iverilog/$(IVERILOG_DIR) ; $(MAKE) install
	$(Q)touch $@

$(PACKAGES_DIR)/$(IVERILOG_TGZ) :
	$(Q)if test ! -d $(PACKAGES_DIR); then mkdir -p $(PACKAGES_DIR); fi
	$(Q)$(WGET) -O $@ $(IVERILOG_URL)
	
$(PREFIX)/etc/install.py : $(PACKAGES_DIR)/install.py
	$(Q)mkdir -p $(PREFIX)/etc
	$(Q)cp $^ $@
	
$(PREFIX)/etc/package.info : $(ETC_DIR)/package.info
	$(Q)mkdir -p $(PREFIX)/etc
	$(Q)cp $^ $@
	
$(PREFIX)/etc/modulefile : $(ETC_DIR)/modulefile
	$(Q)mkdir -p $(PREFIX)/etc
	$(Q)sed -e "s/{{version}}/$(version)/g" $^ > $@
	
$(PACKAGES_DIR)/install.py : 
	$(Q)mkdir -p $(PACKAGES_DIR)
	$(Q)$(WGET) -O $@ $(EDAPACK_BUILD_URL)/raw/master/scripts/install.py

release : $(PACKAGE) $(PACKAGES_DIR)/upload.py
	$(Q)$(PROJECT_DIR)/scripts/upload.py \
		--org EDAPack --repo iverilog \
		--key $(GITHUB_API_TOKEN) --version $(version) \
		$(BUILD_DIR)/iverilog-$(platform)-$(version).tar.gz

$(PACKAGES_DIR)/upload.py :
	$(Q)mkdir -p $(PACKAGES_DIR)
	$(Q)$(WGET) -O $@ $(EDAPACK_BUILD_URL)/raw/master/scripts/upload.py

clean : 
	$(Q)rm -rf $(BUILD_DIR)

clean-all : 
	$(Q)rm -rf $(BUILD_DIR) $(PACKAGES_DIR)
