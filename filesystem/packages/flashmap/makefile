.DEFAULT_GOAL := default
#import all shared make vars
PRAWNOS_ROOT := $(shell git rev-parse --show-toplevel)
include $(PRAWNOS_ROOT)/scripts/BuildScripts/BuildCommon.mk

#package specific vars
VERSION := $(shell cd src/; dpkg-parsechangelog --show-field Version)
PACKAGE_NAME := flashmap_$(VERSION)_$(TARGET_ARCH)
PACKAGE_NAME_DEB := $(PACKAGE_NAME).deb

PACKAGE_LOCAL_BUILD_DEPS :=


default: $(PRAWNOS_LOCAL_APT_REPO)/$(PACKAGE_NAME_DEB)

$(PRAWNOS_LOCAL_APT_REPO)/$(PACKAGE_NAME_DEB): $(PACKAGE_NAME_DEB) | $(PRAWNOS_LOCAL_APT_REPO)
	$(PRAWNOS_PACKAGE_SCRIPTS_INSTALL) $(PACKAGE_NAME_DEB) $(PRAWNOS_LOCAL_APT_REPO)


$(PACKAGE_NAME_DEB):
	$(PRAWNOS_PACKAGE_SCRIPTS_PBUILD) $(PACKAGE_NAME_DEB) \
                                            $(PBUILDER_VARS) \
                                            $(PRAWNOS_LOCAL_APT_VARS) \
                                            $(PACKAGE_LOCAL_BUILD_DEPS)

.PHONY: clean
clean:
	rm -rf $(PRAWNOS_LOCAL_APT_REPO)/$(PACKAGE_NAME_DEB)
	rm -rf *.upload
	rm -rf *.deb
	rm -rf *.changes
	rm -rf *.build
	rm -rf *.diff.gz
	rm -rf *.debian.tar.xz
	rm -rf *.buildinfo
	rm -rf src/debian/.debhelper/
	rm -rf src/debian/$(PACKAGE_NAME)
	rm -rf src/debian/debhelper-build-stamp
	rm -rf src/debian/files

install:
	$(PRAWNOS_PACKAGE_SCRIPTS_INSTALL) $(PACKAGE_NAME_DEB) $(INSTALL_TARGET)

.PHONY: upload
upload:
	$(PRAWNOS_PACKAGE_SCRIPTS_UPLOAD) $(PACKAGE_NAME).changes
