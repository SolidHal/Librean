.DEFAULT_GOAL := default
#import all shared make vars
PRAWNOS_ROOT := $(shell git rev-parse --show-toplevel)
include $(PRAWNOS_ROOT)/scripts/BuildScripts/BuildCommon.mk

ifneq ($(TARGET),$(PRAWNOS_ARM64_RK3588_SERVER))
$(error Cant build arm64 rk3588 server bootloader package with non arm64 rk3588 server target)
endif

#package specific vars
DEB_VERSION := $(PRAWNOS_BOOTLOADER_IMAGE_CAT_DEB_VER)
PRAWNOS_VERSION := $(PRAWNOS_BOOTLOADER_IMAGE_CAT_PRAWNOS_VER)
PACKAGE := prawnos-uboot-image-arm64-rk3588-server
PACKAGE_ARCH := arm64

#uses the full debian version for the package name
PACKAGE_NAME :=  $(PACKAGE)_$(DEB_VERSION)_$(PACKAGE_ARCH)
PACKAGE_NAME_DEB := $(PACKAGE_NAME).deb
#uses only the upstream bootloader version for the orig tar
PACKAGE_NAME_ORIG := $(PACKAGE)_$(PRAWNOS_VERSION)
PACKAGE_NAME_ORIG_TAR = $(PACKAGE_NAME_ORIG).orig.tar.gz
PACKAGE_NAME_ORIG_DIR = $(PACKAGE)_$(BOOTLOADERVER)-$(PRAWNOS_BOOTLOADER_VER)-orig

ORIG := $(PRAWNOS_BUILD)/$(PACKAGE_NAME_ORIG_DIR)

default: $(PACKAGE_NAME_DEB)


$(PACKAGE_NAME_DEB): $(PACKAGE_NAME_ORIG_TAR)
	@echo Building $@
	$(MAKE) clean
	$(PRAWNOS_PACKAGE_SCRIPTS_UPDATE_DEB_FILES) $(PRAWNOS_BOOTLOADER_UBOOT_PACKAGE_IMAGE)/src $(DEB_VERSION) $(PRAWNOS_VERSION) $(PRAWNOS_BOOTLOADER_UBOOT_PACKAGE_IMAGE)
	$(PRAWNOS_PACKAGE_SCRIPTS_PBUILD) $(PACKAGE_NAME_DEB) \
                                            $(PBUILDER_VARS) \
                                            $(PRAWNOS_LOCAL_APT_VARS) \
                                            $(PACKAGE_LOCAL_BUILD_DEPS)


$(PACKAGE_NAME_ORIG_TAR):
	make -f $(PRAWNOS_BOOTLOADER)/makefile bootloader
	rm -rf $(ORIG)
	rm -f $(PACKAGE)_*-*.orig.tar.gz
	mkdir $(ORIG)
	cp $(PRAWNOS_BOOTLOADER_BUILT) $(ORIG)/uboot.img
	cp $(PRAWNOS_BOOTLOADER_RESOURCES)/FlashBootloaderPartition.sh $(ORIG)/
	tar -czf $(PACKAGE_NAME_ORIG_TAR) -C $(ORIG)/ .


.PHONY: clean_all
clean_all: clean clean_orig

.PHONY: clean
clean:
	rm -rf *.upload
	rm -rf *.deb
	rm -rf *.changes
	rm -rf *.dsc
	rm -rf *.build
	rm -rf *.diff.gz
	rm -rf *.debian.tar.xz
	rm -rf *.buildinfo
	rm -f src/debian/debhelper-build-stamp
	rm -rf src/debian/$(PACKAGE)*
	rm -f src/debian/files
	rm -rf src/debian/.debhelper
	rm -rf $(ORIG)

#when just changing the debian package version, if we remake the orig as well then reprepro will complain on upload. So we might not always want to remove the orig.
.PHONY: clean_orig
clean_orig:
	rm -f $(PACKAGE)_*-*.orig.tar.gz

.PHONY: install
install:
	$(PRAWNOS_PACKAGE_SCRIPTS_INSTALL) $(PACKAGE_NAME_DEB) $(INSTALL_TARGET)
	$(PRAWNOS_IMAGE_SCRIPTS_INSTALL_BOOTLOADER) $(PRAWNOS_IMAGE) $(TARGET) $(PRAWNOS_BOOTLOADER_BUILD) $(PRAWNOS_BUILD) $(PACKAGE) $(PACKAGE_NAME_DEB)

.PHONY: upload
upload:
	$(PRAWNOS_PACKAGE_SCRIPTS_UPLOAD) $(PACKAGE_NAME).changes
