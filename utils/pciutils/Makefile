#
# Copyright (C) 2007-2017 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=pciutils
PKG_VERSION:=3.6.2
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@KERNEL/software/utils/pciutils
PKG_HASH:=db452ec986edefd88af0d222d22f6102f8030a8633fdfe846c3ae4bde9bb93f3

PKG_MAINTAINER:=Álvaro Fernández Rojas <noltari@gmail.com>
PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=COPYING

PKG_USE_MIPS16:=0
PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk

define Package/pciutils
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=Linux PCI Utilities
  URL:=http://mj.ucw.cz/pciutils.shtml
  DEPENDS:=+libkmod +zlib
endef

define Package/pciutils/description
 contains collection of programs for inspecting and manipulating configuration
 of PCI devices
endef

PCI_IDS_VER:=0.319
PCI_IDS_FILE:=pci.ids.$(PCI_IDS_VER)
define Download/pci_ids
  FILE:=$(PCI_IDS_FILE)
  URL_FILE:=pci.ids
  URL:=@GITHUB/vcrhonek/hwdata/v$(PCI_IDS_VER)
  HASH:=54154a6955f550b110c6a216943dcd69ba6188e68b80bee6efbaa03ef0df0a5f
endef
$(eval $(call Download,pci_ids))

define Build/Prepare
	$(call Build/Prepare/Default)
	$(RM) $(PKG_BUILD_DIR)/pci.ids
	$(CP) $(DL_DIR)/$(PCI_IDS_FILE) $(PKG_BUILD_DIR)/pci.ids
endef

MAKE_FLAGS += \
	CFLAGS="$(TARGET_CFLAGS) $(TARGET_CPPFLAGS)" \
	PREFIX="/usr" \
	HOST="Linux" \
	HWDB="no" \
	ZLIB="yes"

ifneq ($(CONFIG_USE_GLIBC),)
TARGET_LDFLAGS += -lresolv
endif

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_BUILD_DIR)/lib/libpci.a $(1)/usr/lib
	$(INSTALL_DIR) $(1)/usr/include/pci
	$(CP) $(foreach i,pci.h config.h header.h types.h, \
		$(PKG_BUILD_DIR)/lib/$(i)) $(1)/usr/include/pci
endef

define Package/pciutils/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/{lspci,setpci,update-pciids} $(1)/usr/sbin/
	$(INSTALL_DIR) $(1)/usr/share
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/share/pci.ids.gz $(1)/usr/share/
endef

$(eval $(call BuildPackage,pciutils))
