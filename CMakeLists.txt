CMAKE_MINIMUM_REQUIRED(VERSION 3.13) # 3.13: concise relative source paths

SET(CMAKE_C_STANDARD 99)
SET(CMAKE_C_STANDARD_REQUIRED TRUE)

SET(LIBBTC_NAME btc)
PROJECT(lib${LIBBTC_NAME} VERSION 0.1)

INCLUDE(CTest)
SET(USE_TESTS ${CMAKE_TESTING_ENABLED})
SET(WITH_TOOLS TRUE CACHE BOOL "enable bitcoin tool cli application")
SET(RANDOM_DEVICE "/dev/urandom" CACHE STRING "set the device to read random data from")

MESSAGE(STATUS "")
MESSAGE(STATUS "Options used to compile and link:")
MESSAGE(STATUS "  WITH_TOOLS    = ${WITH_TOOLS}")
MESSAGE(STATUS "")

ADD_DEFINITIONS(
    -DPACKAGE_NAME="${PROJECT_NAME}"
    -DPACKAGE_VERSION="${PROJECT_VERSION}"
    -DRANDOM_DEVICE="${RANDOM_DEVICE}"
)
IF(USE_TESTS)
    ADD_DEFINITIONS(-DUSE_TESTS=1)
ENDIF()
IF(WITH_TOOLS)
    ADD_DEFINITIONS(-DWITH_TOOLS=1)
ENDIF()
FILE(TOUCH src/libbtc-config.h)


ADD_LIBRARY(${LIBBTC_NAME})

INSTALL(FILES
    include/btc/aes256_cbc.h
    include/btc/base58.h
    include/btc/bip32.h
    include/btc/block.h
    include/btc/blockchain.h
    include/btc/btc.h
    include/btc/buffer.h
    include/btc/chainparams.h
    include/btc/cstr.h
    include/btc/ctaes.h
    include/btc/ecc_key.h
    include/btc/ecc.h
    include/btc/hash.h
    include/btc/hmac.h
    include/btc/memory.h
    include/btc/portable_endian.h
    include/btc/random.h
    include/btc/ripemd160.h
    include/btc/script.h
    include/btc/segwit_addr.h
    include/btc/serialize.h
    include/btc/sha2.h
    include/btc/tool.h
    include/btc/tx.h
    include/btc/utils.h
    include/btc/vector.h
    DESTINATION include/btc
)

INSTALL(FILES
    src/trezor-crypto/base58.h
    src/trezor-crypto/blake2_common.h
    src/trezor-crypto/blake2b.h
    src/trezor-crypto/blake256.h
    src/trezor-crypto/groestl.h
    src/trezor-crypto/groestl_internal.h
    src/trezor-crypto/hasher.h
    src/trezor-crypto/hmac.h
    src/trezor-crypto/memzero.h
    src/trezor-crypto/options.h
    src/trezor-crypto/ripemd160.h
    src/trezor-crypto/segwit_addr.h
    src/trezor-crypto/sha2.h
    src/trezor-crypto/sha3.h
    DESTINATION include/trezor-crypto
)

TARGET_SOURCES(${LIBBTC_NAME} PRIVATE
    src/aes256_cbc.c
    src/base58.c
    src/bip32.c
    src/block.c
    src/buffer.c
    src/chainparams.c
    src/commontools.c
    src/cstr.c
    src/ctaes.c
    src/ecc_key.c
    src/ecc_libsecp256k1.c
    src/memory.c
    src/random.c
    src/ripemd160.c
    src/script.c
    src/serialize.c
    src/tx.c
    src/utils.c
    src/vector.c
)

TARGET_SOURCES(${LIBBTC_NAME} PRIVATE
    src/trezor-crypto/base58.c
    src/trezor-crypto/blake2b.c
    src/trezor-crypto/blake256.c
    src/trezor-crypto/groestl.c
    src/trezor-crypto/hasher.c
    src/trezor-crypto/hmac.c
    src/trezor-crypto/memzero.c
    src/trezor-crypto/ripemd160.c
    src/trezor-crypto/segwit_addr.c
    src/trezor-crypto/sha2.c
    src/trezor-crypto/sha3.c
)

FILE(GLOB SECP256K1 RELATIVE ${CMAKE_CURRENT_SOURCE_DIR}
    src/secp256k1/src/*.c)
LIST(REMOVE_ITEM SECP256K1
    src/secp256k1/src/tests.c
    src/secp256k1/src/tests_exhaustive.c
    src/secp256k1/src/bench_ecdh.c
    src/secp256k1/src/bench_ecmult.c
    src/secp256k1/src/bench_recover.c
    src/secp256k1/src/bench_sign.c
    src/secp256k1/src/bench_verify.c
    src/secp256k1/src/bench_ecdh.c
    src/secp256k1/src/bench_internal.c
    src/secp256k1/src/bench_schnorr_verify.c
    src/secp256k1/src/valgrind_ctime_test.c)
ADD_DEFINITIONS(
    -DUSE_NUM_GMP
    -DUSE_FIELD_10X26
    -DUSE_FIELD_INV_BUILTIN
    -DUSE_SCALAR_8X32
    -DUSE_SCALAR_INV_BUILTIN
    -DENABLE_MODULE_RECOVERY
    -DECMULT_WINDOW_SIZE=15
    -DECMULT_GEN_PREC_BITS=4)
TARGET_SOURCES(${LIBBTC_NAME} PRIVATE ${SECP256K1})

INCLUDE_DIRECTORIES(
    include
    src/secp256k1
    ${GMP_INSTALL_DIR}/include)

IF(USE_TESTS)
    ADD_EXECUTABLE(tests)
    TARGET_SOURCES(tests PRIVATE
        test/aes_tests.c
        test/base58check_tests.c
        test/bip32_tests.c
        test/block_tests.c
        test/buffer_tests.c
        test/cstr_tests.c
        test/ecc_tests.c
        test/eckey_tests.c
        test/hash_tests.c
        test/memory_tests.c
        test/random_tests.c
        test/serialize_tests.c
        test/sha2_tests.c
        test/unittester.c
        test/tx_tests.c
        test/utils_tests.c
        test/vector_tests.c
    )
    TARGET_LINK_LIBRARIES(tests ${LIBBTC_NAME})
    ADD_TEST(NAME ${LIBBTC_NAME}_tests COMMAND tests)
ENDIF()

IF(WITH_TOOLS)
    IF(USE_TESTS)
        TARGET_SOURCES(tests PRIVATE
            test/tool_tests.c
        )
    ENDIF()

    ADD_EXECUTABLE(bitcointool src/tools/bitcointool.c)
    INSTALL(TARGETS bitcointool RUNTIME)
    TARGET_LINK_LIBRARIES(bitcointool ${LIBBTC_NAME})
    TARGET_INCLUDE_DIRECTORIES(bitcointool PRIVATE src)

ENDIF()

