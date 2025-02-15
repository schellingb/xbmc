include ../../Makefile.include
DEPS = ../../Makefile.include Makefile ../../download-files.include \
                              add-dl-as-private-lib.patch \
                              01-macos-clang-accel.patch 03-support-correct-cisdigit.patch

# lib name, version
LIBNAME=gnutls
VERSION=3.6.16
SOURCE=$(LIBNAME)-$(VERSION)
ARCHIVE=$(SOURCE).tar.xz
SHA512=72c78d7fcb024393c1d15f2a1856608ae4460ba43cc5bbbb4c29b80508cae6cb822df4638029de2363437d110187e0a3cc19a7288c3b2f44b2f648399a028438
include ../../download-files.include

# configuration settings
CONFIGURE=./configure --prefix=$(PREFIX) \
                      --disable-shared \
                      --without-p11-kit \
                      --disable-nls \
                      --with-included-unistring \
                      --with-included-libtasn1 \
                      --enable-local-libopts \
                      --disable-doc \
                      --disable-tests \
                      --disable-guile \
                      --disable-tools \
                      --without-idn \
                      $(CONFIGURE_OPTIONS)

LIBDYLIB=$(PLATFORM)/lib/.libs/lib$(LIBNAME).a

all: .installed-$(PLATFORM)

$(PLATFORM): $(DEPS) | $(TARBALLS_LOCATION)/$(ARCHIVE).$(HASH_TYPE)
	rm -rf $(PLATFORM)/*; mkdir -p $(PLATFORM)
	cd $(PLATFORM); $(ARCHIVE_TOOL) $(ARCHIVE_TOOL_FLAGS) $(TARBALLS_LOCATION)/$(ARCHIVE)
	cd $(PLATFORM); patch -p1 -i ../add-dl-as-private-lib.patch
ifeq ($(findstring apple-darwin, $(HOST)), apple-darwin)
	cd $(PLATFORM); patch -p1 -i ../01-macos-clang-accel.patch
endif
	cd $(PLATFORM); patch -p1 -i ../03-support-correct-cisdigit.patch
	cd $(PLATFORM); $(AUTORECONF) -vif
	cd $(PLATFORM); $(CONFIGURE)

$(LIBDYLIB): $(PLATFORM)
	$(MAKE) -C $(PLATFORM)

.installed-$(PLATFORM): $(LIBDYLIB)
	$(MAKE) -C $(PLATFORM) install
	touch $@

clean:
	$(MAKE) -C $(PLATFORM) clean
	rm -f .installed-$(PLATFORM)

distclean::
	rm -rf $(PLATFORM) .installed-$(PLATFORM)
