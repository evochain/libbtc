ACLOCAL_AMFLAGS = -I build-aux/m4
AUTOMAKE_OPTIONS = serial-tests
.PHONY: gen
.INTERMEDIATE: $(GENBIN)

DIST_SUBDIRS = src/secp256k1

LIBSECP256K1=src/secp256k1/libsecp256k1.la

$(LIBSECP256K1): $(wildcard src/secp256k1/src/*) $(wildcard src/secp256k1/include/*)
	$(AM_V_at)$(MAKE) $(AM_MAKEFLAGS) -C $(@D) $(@F)

lib_LTLIBRARIES = libbtc.la
libbtcincludedir = $(includedir)/btc
libbtcinclude_HEADERS = \
    include/btc/aes256_cbc.h \
    include/btc/base58.h \
    include/btc/bip32.h \
    include/btc/block.h \
    include/btc/blockchain.h \
    include/btc/btc.h \
    include/btc/buffer.h \
    include/btc/chainparams.h \
    include/btc/cstr.h \
    include/btc/ctaes.h \
    include/btc/ecc_key.h \
    include/btc/ecc.h \
    include/btc/hash.h \
    include/btc/hmac.h \
    include/btc/memory.h \
    include/btc/portable_endian.h \
    include/btc/random.h \
    include/btc/ripemd160.h \
    include/btc/script.h \
    include/btc/segwit_addr.h \
    include/btc/serialize.h \
    include/btc/sha2.h \
    include/btc/tool.h \
    include/btc/tx.h \
    include/btc/utils.h \
    include/btc/vector.h

trezorcryptoincludedir = $(includedir)/trezor-crypto
trezorcryptoinclude_HEADERS = \
    src/trezor-crypto/base58.h \
    src/trezor-crypto/blake2_common.h \
    src/trezor-crypto/blake2b.h \
    src/trezor-crypto/blake256.h \
    src/trezor-crypto/groestl.h \
    src/trezor-crypto/groestl_internal.h \
    src/trezor-crypto/hasher.h \
    src/trezor-crypto/hmac.h \
    src/trezor-crypto/memzero.h \
    src/trezor-crypto/options.h \
    src/trezor-crypto/ripemd160.h \
    src/trezor-crypto/segwit_addr.h \
    src/trezor-crypto/sha2.h \
    src/trezor-crypto/sha3.h

pkgconfigdir = $(libdir)/pkgconfig
pkgconfig_DATA = libbtc.pc

libbtc_la_SOURCES = \
    src/aes256_cbc.c \
    src/base58.c \
    src/bip32.c \
    src/block.c \
    src/buffer.c \
    src/chainparams.c \
    src/commontools.c \
    src/cstr.c \
    src/ctaes.c \
    src/ecc_key.c \
    src/ecc_libsecp256k1.c \
    src/memory.c \
    src/random.c \
    src/ripemd160.c \
    src/script.c \
    src/serialize.c \
    src/tx.c \
    src/utils.c \
    src/vector.c

libbtc_la_SOURCES += \
    src/trezor-crypto/base58.c \
    src/trezor-crypto/blake2b.c \
    src/trezor-crypto/blake256.c \
    src/trezor-crypto/groestl.c \
    src/trezor-crypto/hasher.c \
    src/trezor-crypto/hmac.c \
    src/trezor-crypto/memzero.c \
    src/trezor-crypto/ripemd160.c \
    src/trezor-crypto/segwit_addr.c \
    src/trezor-crypto/sha2.c \
    src/trezor-crypto/sha3.c

libbtc_la_CFLAGS = -I$(top_srcdir)/include 
libbtc_la_LIBADD = $(LIBSECP256K1)

if USE_TESTS
noinst_PROGRAMS = tests
tests_LDADD = libbtc.la
tests_SOURCES = \
    test/aes_tests.c \
    test/base58check_tests.c \
    test/bip32_tests.c \
    test/block_tests.c \
    test/buffer_tests.c \
    test/cstr_tests.c \
    test/ecc_tests.c \
    test/eckey_tests.c \
    test/hash_tests.c \
    test/memory_tests.c \
    test/random_tests.c \
    test/serialize_tests.c \
    test/sha2_tests.c \
    test/utest.h \
    test/unittester.c \
    test/tx_tests.c \
    test/utils_tests.c \
    test/vector_tests.c

tests_CFLAGS = $(libbtc_la_CFLAGS)
tests_CPPFLAGS = -I$(top_srcdir)/src
tests_LDFLAGS = -static
TESTS = tests
endif

if WITH_TOOLS
if USE_TESTS
tests_SOURCES += \
    test/tool_tests.c
endif
instdir=$(prefix)/bin
inst_PROGRAMS = bitcointool
bitcointool_LDADD = libbtc.la
bitcointool_SOURCES = \
    src/tools/bitcointool.c
bitcointool_CFLAGS = $(libbtc_la_CFLAGS)
bitcointool_CPPFLAGS = -I$(top_srcdir)/src
bitcointool_LDFLAGS = -static

endif

clean-local:
	-$(MAKE) -C src/secp256k1 clean
