
SUBDIRS = src

EXTRA_DIST=autogen.sh
check_targets = check-plain check-flags check-minimize-tags check-tags check-minimize check-single
pkgconfigdir = $(libdir)/pkgconfig
dist_pkgconfig_DATA = lexd.pc

timing-test: all
	(cd tests || exit && ./timing.sh wad && ./timing.sh heb)
check: $(check_targets)
test: check
check-clean:
	+ make -C tests/feature clean

$(check_targets): check-%: all tests/feature
	+ make -C tests/feature O=$* LEXD_TEST_FLAGS="$$(echo '$*' | grep -v plain | sed 's/^\|-/ --/g')" check
	+ make -C tests/feature O=$* clean
