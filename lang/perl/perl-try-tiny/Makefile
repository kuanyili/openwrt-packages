#
# Copyright (C) 2021 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=perl-try-tiny
PKG_VERSION:=0.32
PKG_RELEASE:=1

PKG_SOURCE_URL:=https://cpan.metacpan.org/authors/id/E/ET/ETHER/
PKG_SOURCE:=Try-Tiny-$(PKG_VERSION).tar.gz
PKG_HASH:=ef2d6cab0bad18e3ab1c4e6125cc5f695c7e459899f512451c8fa3ef83fa7fc0
PKG_BUILD_DIR:=$(BUILD_DIR)/perl/Try-Tiny-$(PKG_VERSION)

PKG_MAINTAINER:=Matt Merhar <mattmerhar@protonmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk
include ../perlmod.mk

define Package/perl-try-tiny
  SUBMENU:=Perl
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Minimal try/catch with proper preservation of $$$$@
  URL:=https://metacpan.org/pod/Try::Tiny
  DEPENDS:=perl +perlbase-essential
endef

define Build/Configure
	$(call perlmod/Configure,,)
endef

define Build/Compile
	$(call perlmod/Compile,,)
endef

define Package/perl-try-tiny/install
	$(call perlmod/Install,$(1),Try auto/Try)
endef

$(eval $(call BuildPackage,perl-try-tiny))
