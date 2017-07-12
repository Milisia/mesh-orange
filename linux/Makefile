#
# Download a pre-built Linux kernel
#

# TODO
# - Add a submodule for the kernel repo and allow a local build to produce the
#   expected tar.xz file
# - have a tool that takes the tar.xz file and can publish it as a release
#   on github using API calls

# FIXME
# - this is currently single arch - need to rename the tar file and the
#   extraction dir

# Its not really "debian" arch, but this is the same var name as elsewhere
DEBIAN_ARCH = armhf

# Where should we look for the linux.tar.gz file?
REPO = https://github.com/hamishcoleman/linux
RELEASE_TAG = mvp4.12

# Standardised directory names
BUILD = build
TAG = $(BUILD)/tags

URL = $(REPO)/releases/download/$(RELEASE_TAG)/linux.tar.xz # FIXME - arch

DTBS = sun8i-h2-plus-orangepi-zero.dtb

BUILD_DEPENDS = xz-utils wget

# install any packages needed for the builder
build-depends: $(TAG)/build-depends
$(TAG)/build-depends: Makefile
	sudo apt-get -y install $(BUILD_DEPENDS)
	$(call tag,build-depends)

CLEAN_FILES += $(BUILD)/linux.tar.xz # FIXME - arch
$(BUILD)/linux.tar.xz: Makefile # FIXME - arch
	mkdir -p $(dir $@)
	wget -c -O $@ $(URL)
	touch $@

CLEAN_FILES += $(BUILD)/linux # FIXME - arch
$(BUILD)/linux/zImage $(BUILD)/linux/lib/modules $(addprefix $(BUILD)/linux/dtb/,$(DTBS)): $(BUILD)/linux.tar.xz
	tar -m -x -f $< -C $(BUILD)

# Add the kernel specific binaries to this cpio file
$(BUILD)/modules-$(DEBIAN_ARCH).cpio: $(BUILD)/linux/lib/modules # FIXME - arch
	( \
            cd $(BUILD)/linux; \
            find lib/modules -print0 | cpio -0 -H newc -R 0:0 -o \
	) > $@

# Generic makefile rules below here

clean:
	rm -rf $(CLEAN_FILES)

reallyclean:
	rm -rf $(BUILD)

define tag
	@echo Touching tag $1
	@mkdir -p $(TAG)
	@touch $(TAG)/$1
endef
