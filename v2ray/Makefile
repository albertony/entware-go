#
# Copyright (C) 2021 Entware
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=v2ray
PKG_VERSION:=4.45.2
PKG_RELEASE:=1
PKG_GOGET:=github.com/v2fly/v2ray-core

GOPKG_SOURCE=GO_$(PKG_NAME)-$(PKG_VERSION)-$(PKG_RELEASE).tar.bz2

#!!! uncomment PKG_SOURCE to download fixed version
PKG_SOURCE:=$(GOPKG_SOURCE)
PKG_HASH:=9e9843f3179871c3b592f365d9968880f883ffc94d504461048ae4d7ce3caabc
PKG_COMMIT:=1c6e4bbf6cabfcd252ce72da7d5a771f28e128d8

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/$(PKG_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/golang.mk

ifeq ($(BUILD_VARIANT),nohf)
GOARM=GOARM=5
endif

define Package/v2ray/Default
	$(call Package/gopackage/Default)
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Web Servers/Proxies
	DEPENDS:=+ca-bundle
	TITLE:=proxy building platform
	URL:=https://github.com/v2fly/v2ray-core
endef

define Package/v2ray
	$(call Package/v2ray/Default)
	VARIANT:=hf
endef

define Package/v2ray_nohf
	$(call Package/v2ray/Default)
	VARIANT:=nohf
	DEPENDS:=@(TARGET_armv7_2_6||TARGET_armv7_3_2)
endef

define Package/v2ray/description
 A platform for building proxies to bypass network restrictions.
endef

Package/v2ray_nohf/description=$(Package/v2ray/description)

define Build/Compile
	(\
		cd $(PKG_BUILD_DIR); \
		mkdir -p bin; \
		cd src/$(PKG_GOGET); \
		CGO_ENABLED=0 GO111MODULE=on GOOS=linux GOARCH=$(GOARCH) $(GOARM) GOPATH=$(PKG_BUILD_DIR) $(GOROOT)/bin/go build -ldflags="-X main.Build=$(PKG_VERSION) -s -w" -x -v -o ${PKG_BUILD_DIR}/bin/${PKG_NAME} $(PKG_GOGET)/v4/main; \
		CGO_ENABLED=0 GO111MODULE=on GOOS=linux GOARCH=$(GOARCH) $(GOARM) GOPATH=$(PKG_BUILD_DIR) $(GOROOT)/bin/go build -tags confonly -ldflags="-X main.Build=$(PKG_VERSION) -s -w" -x -v -o ${PKG_BUILD_DIR}/bin/v2ctl $(PKG_GOGET)/v4/infra/control/main; \
	        chmod -R +w $(PKG_BUILD_DIR)/pkg; \
	)
endef

define Package/v2ray/conffiles
/opt/etc/v2ray/config.json
/opt/etc/v2ray/vpoint_socks_vmess.json
/opt/etc/v2ray/vpoint_vmess_freedom.json

endef

define Package/v2ray/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/bin/v2ray $(1)/opt/sbin
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/bin/v2ctl $(1)/opt/bin
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) files/S24v2ray $(1)/opt/etc/init.d
	$(INSTALL_DIR) $(1)/opt/etc/v2ray
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/src/$(PKG_GOGET)/release/config/{config,vpoint_socks_vmess,vpoint_vmess_freedom}.json \
		$(1)/opt/etc/v2ray
endef

Package/v2ray_nohf/install=$(Package/v2ray/install)

$(eval $(call BuildPackage,v2ray))
$(eval $(call BuildPackage,v2ray_nohf))

