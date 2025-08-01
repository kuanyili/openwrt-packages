# Copyright (C) 2009-2019 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=nfs-kernel-server
PKG_VERSION:=2.8.3
PKG_RELEASE:=2
PKG_HASH:=11e7c5847a8423a72931c865bd9296e7fd56ff270a795a849183900961711725

PKG_SOURCE_URL:=@KERNEL/linux/utils/nfs-utils/$(PKG_VERSION)
PKG_SOURCE:=nfs-utils-$(PKG_VERSION).tar.xz

HOST_BUILD_DIR:=$(BUILD_DIR_HOST)/nfs-utils-$(PKG_VERSION)
PKG_BUILD_DIR:=$(BUILD_DIR)/nfs-utils-$(PKG_VERSION)
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1
PKG_BUILD_DEPENDS:=$(PKG_NAME)/host libevent2
HOST_BUILD_DEPENDS:=libtirpc/host

PKG_CONFIG_DEPENDS:= \
	CONFIG_IPV6 \
	CONFIG_NFS_KERNEL_SERVER_V4

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

define Package/nfs-kernel-server/Default
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Filesystem
	DEPENDS:=+libblkid +libuuid +libtirpc +lsqlite3 +libxml2 +libnl
	URL:=http://nfs.sourceforge.net/
endef

define Package/nfs-kernel-server
	$(call Package/nfs-kernel-server/Default)
	TITLE:=Kernel NFS server support
	DEPENDS+= +kmod-fs-nfsd +kmod-fs-nfs +NFS_KERNEL_SERVER_V4:kmod-fs-nfs-v4 +rpcbind +NFS_KERNEL_SERVER_V4:nfs-utils-libs +NFS_KERNEL_SERVER_V4:libkeyutils +NFS_KERNEL_SERVER_V4:libdevmapper
	USERID:=nfs:nfs
endef

define Package/nfs-kernel-server/description
Kernel NFS server support
endef

define Package/nfs-kernel-server/config
	source "$(SOURCE)/Config.in"
endef

define Package/nfs-kernel-server-utils
	$(call Package/nfs-kernel-server/Default)
	TITLE:=NFS server utils
	DEPENDS:=nfs-kernel-server
endef

define Package/nfs-kernel-server-utils/description
NFS server utils
endef

define Package/nfs-kernel-server/conffiles
/etc/exports
endef

define Package/nfs-utils/Default
	$(call Package/nfs-kernel-server/Default)
	SECTION:=utils
	CATEGORY:=Utilities
	DEPENDS+= +NFS_KERNEL_SERVER_V4:libkeyutils +NFS_KERNEL_SERVER_V4:libdevmapper
	URL:=http://nfs.sourceforge.net/
endef

define Package/nfs-utils
	$(call Package/nfs-utils/Default)
	TITLE:=updated mount utility (includes nfs4)
endef

define Package/nfs-utils/description
Updated mount.nfs command - allows mounting nfs4 volumes
endef

define Package/nfs-utils-libs
	$(call Package/nfs-utils/Default)
	TITLE:=libraries provided by nfs-utils
endef

define Package/nfs-utils-libs/description
Libraries provided by nfs-utils
endef

TARGET_CFLAGS += -Wno-error=implicit-function-declaration \
		 -Wno-error=strict-prototypes \
		 -Wno-error=incompatible-pointer-types \
		 -Wno-error=format-security \
		 -Wno-error=undef \
		 -Wno-error=missing-include-dirs

ifneq ($(CONFIG_USE_MUSL),)
  TARGET_CFLAGS += -D_LARGEFILE64_SOURCE
endif

TARGET_LDFLAGS += -L$(STAGING_DIR)/usr/lib/libevent

CONFIGURE_ARGS += \
	--disable-caps \
	--disable-gss \
	--disable-nfsdcld \
	--disable-nfsdcltrack \
	--enable-shared \
	--enable-static \
	--with-rpcgen=internal \
	$(if $(CONFIG_NFS_KERNEL_SERVER_V4),--enable,--disable)-nfsv4 \
	$(if $(CONFIG_NFS_KERNEL_SERVER_V4),--enable,--disable)-nfsv41 \
	$(if $(CONFIG_NFS_KERNEL_SERVER_V4),--enable,--disable)-nfsv42

ifeq ($(CONFIG_IPV6),n)
	CONFIGURE_ARGS += --disable-ipv6
endif

CONFIGURE_VARS += \
	libblkid_cv_is_recent=yes \
	ac_cv_lib_resolv___res_querydomain=yes \
	ac_cv_lib_bsd_daemon=no \
	CONFIG_SQLITE3_TRUE="\#" \
	CONFIG_NFSDCLD_TRUE="\#"

MAKE_FLAGS += \
	OPT="$(TARGET_CFLAGS)" \
	INSTALLSUID="install -m 4755" \
	DESTDIR="$(PKG_INSTALL_DIR)" \
	RPCGEN_PATH=$(STAGING_DIR_HOSTPKG)/bin/rpcgen \
	RPCGEN=$(STAGING_DIR_HOSTPKG)/bin/rpcgen

HOST_CFLAGS += -Dlinux \
	-Wno-error=missing-include-dirs

HOST_CONFIGURE_ARGS += \
	--disable-gss \
	--disable-nfsv4 \
	--disable-nfsv41 \
	--disable-nfsrahead \
	--disable-nfsdctl \
	--without-tcp-wrappers \
	--disable-junction \
	--with-rpcgen=internal

HOST_CONFIGURE_VARS += \
	ac_cv_lib_event_event_dispatch=yes \
	ac_cv_lib_nfsidmap_nfs4_init_name_mapping=yes \
	ac_cv_lib_blkid_blkid_get_library_version=yes \
	ac_cv_header_event_h=yes \
	ac_cv_header_nfsidmap_h=yes \
	ac_cv_header_blkid_blkid_h=yes \
	ac_cv_lib_resolv___res_querydomain=yes \
	ac_cv_func_prctl=yes \
	ac_cv_sizeof_size_t=0 \
	ac_cv_func_getrpcbynumber=yes \
	ac_cv_func_getrpcbynumber_r=yes \
	enable_ipv6=no \
	enable_nfsdctl=no \
	GSSGLUE_CFLAGS=" " \
	GSSGLUE_LIBS=" " \
	RPCSECGSS_CFLAGS=" " \
	RPCSECGSS_LIBS=" " \
	CONFIG_SQLITE3_TRUE="\#" \
	CONFIG_NFSDCLD_TRUE="\#" \
	ac_cv_lib_event_core_event_base_dispatch=yes \
	ac_cv_header_event2_event_h=yes \
	c_cv_lib_sqlite3_sqlite3_libversion_number=yes \
	libsqlite3_cv_is_recent=yes

define Host/Compile
	$(MAKE) -C $(HOST_BUILD_DIR)/tools/rpcgen all
endef

define Host/Install
	$(INSTALL_DIR) $(STAGING_DIR_HOSTPKG)/bin
	$(INSTALL_BIN) $(HOST_BUILD_DIR)/tools/rpcgen/rpcgen $(STAGING_DIR_HOSTPKG)/bin/rpcgen
endef

define Package/nfs-kernel-server/install
	$(INSTALL_DIR) $(1)/etc/init.d $(1)/usr/sbin
	$(INSTALL_DATA) ./files/nfsd.exports $(1)/etc/exports
	$(INSTALL_BIN) ./files/nfsd.init $(1)/etc/init.d/nfsd
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/statd/sm-notify $(1)/usr/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/statd/statd $(1)/usr/sbin/rpc.statd
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/nfsd/nfsd $(1)/usr/sbin/rpc.nfsd
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/mountd/mountd $(1)/usr/sbin/rpc.mountd
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/exportfs/exportfs $(1)/usr/sbin/
endef

define Package/nfs-kernel-server-utils/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/showmount/showmount $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/nfsstat/nfsstat $(1)/usr/sbin
endef

define Package/nfs-utils/InstallDev
	$(INSTALL_DIR) $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include/nfsidmap.h $(1)/usr/include/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnfsidmap.{a,la,so*} $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/libnfsidmap.pc $(1)/usr/lib/pkgconfig/
endef

define Package/nfs-utils/install
	$(INSTALL_DIR) $(1)/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/sbin/mount.nfs $(1)/sbin/
	(cd $(1)/sbin; ln -sf mount.nfs mount.nfs4; ln -sf mount.nfs umount.nfs; ln -sf mount.nfs umount.nfs4)
endef

define Package/nfs-utils-libs/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(if $(CONFIG_NFS_KERNEL_SERVER_V4),$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnfsidmap.so* $(1)/usr/lib/,)
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,nfs-utils))
$(eval $(call BuildPackage,nfs-utils-libs))
$(eval $(call BuildPackage,nfs-kernel-server))
$(eval $(call BuildPackage,nfs-kernel-server-utils))
