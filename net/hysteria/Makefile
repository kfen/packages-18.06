# SPDX-License-Identifier: GPL-3.0-only
#
# Copyright (C) 2021 ImmortalWrt.org

include $(TOPDIR)/rules.mk

PKG_NAME:=hysteria
PKG_VERSION:=0.9.0
PKG_RELEASE:=$(AUTORELEASE)

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/HyNetwork/hysteria/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=5dd018b16e4fb24799a4df4a8cc1e6db94752ec010d008b7b6ca96bd8b4df4d6

PKG_LICENSE:=MIT
PKG_LICENSE_FILE:=LICENSE
PKG_MAINTAINER:=Tianling Shen <cnsztl@immortalwrt.org>

PKG_CONFIG_DEPENDS:= \
	CONFIG_HYSTERIA_COMPRESS_GOPROXY \
	CONFIG_HYSTERIA_COMPRESS_UPX

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

GO_PKG:=github.com/tobyxdd/hysteria
GO_PKG_BUILD_PKG:=github.com/tobyxdd/hysteria/cmd
GO_PKG_LDFLAGS:=-s -w
GO_PKG_LDFLAGS_X:=main.appVersion=$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include ../../lang/golang/golang-package.mk

define Package/hysteria
  SECTION:=net
  CATEGORY:=Network
  TITLE:=A feature-packed network utility optimized for networks of poor quality
  URL:=https://github.com/tobyxdd/hysteria
  DEPENDS:=$(GO_ARCH_DEPENDS) +ca-bundle
endef

define Package/hysteria/description
  Hysteria is a feature-packed network utility optimized for networks
  of poor quality (e.g. satellite connections, congested public Wi-Fi,
  connecting from China to servers abroad) powered by a custom version
  of QUIC protocol.
endef

define Package/hysteria/config
config HYSTERIA_COMPRESS_GOPROXY
	bool "Compiling with GOPROXY proxy"
	default n

config HYSTERIA_COMPRESS_UPX
	bool "Compress executable files with UPX"
	depends on !mips64
	default n
endef

ifeq ($(CONFIG_HYSTERIA_COMPRESS_GOPROXY),y)
	export GO111MODULE=on
	export GOPROXY=https://goproxy.baidu.com
endif

define Build/Compile
	$(call GoPackage/Build/Compile)
ifeq ($(CONFIG_HYSTERIA_COMPRESS_UPX),y)
	$(STAGING_DIR_HOST)/bin/upx --lzma --best $(GO_PKG_BUILD_BIN_DIR)/cmd
endif
endef

define Package/hysteria/install
	$(call GoPackage/Package/Install/Bin,$(PKG_INSTALL_DIR))
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/cmd $(1)/usr/bin/hysteria
endef

$(eval $(call GoBinPackage,hysteria))
$(eval $(call BuildPackage,hysteria))
