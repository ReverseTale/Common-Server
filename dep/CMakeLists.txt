#######################
# LIBBSON

    set(LIBBSON_CONFIGURE
        "-DENABLE_TESTS=OFF"
    )

    RequireExternal(
        TARGET ReverseTale-CommonServer
        MODULE "mongodb/libbson:r1.9"
        CONFIGURE_ARGUMENTS ${LIBBSON_CONFIGURE}
    )

#######################
# MONGOC 

    set(MONGOC_CONFIGURE
        "-DENABLE_SASL=OFF"     # TODO: Revisit this, do we want it?
        "-DENABLE_SSL=OFF"      # TODO: Revisit this, do we want it?
        "-DENABLE_TESTS=OFF"
        "-DENABLE_EXAMPLES=OFF"
    )

    RequireExternal(
        TARGET ReverseTale-CommonServer
        ENSURE_ORDER
        MODULE "mongodb/mongo-c-driver:r1.9"
        CONFIGURE_ARGUMENTS ${MONGOC_CONFIGURE}
    )

#######################
# MONGOCXX

    set(MONGOCXX_CONFIGURE
        "-DENABLE_TESTS=OFF"     # TODO: No longer can be disabled
        "-DENABLE_EXAMPLES=OFF"  # TODO: No longer can be disabled
    )

    RequireExternal(
        TARGET ReverseTale-CommonServer
        ENSURE_ORDER
        MODULE "mongodb/mongo-cxx-driver:r3.2.0"
        CONFIGURE_ARGUMENTS ${MONGOCXX_CONFIGURE}
    )
    
#######################
# INIH
    set(INIH_PATCHES
        "git apply --whitespace=fix ${CMAKE_SOURCE_DIR}/cmake/Patches/01-inih-cmake.patch"
        "git apply --whitespace=fix ${CMAKE_SOURCE_DIR}/cmake/Patches/02-inih-has_key.patch"
    )

    RequireExternal(
        TARGET ReverseTale-CommonServer
        MODULE "benhoyt/inih:0c3f8ea80ca2c8be8995a0b7b4e84af1641a4dd2"
        CONFIGURE_STEPS ${INIH_PATCHES}
    )

#######################
# THREADPOOL11

    RequireExternal(
        TARGET ReverseTale-CommonServer
        MODULE "gpascualg/threadpool11:2.0"
    )

#######################
# ReverseTale-Library

    RequireExternal(
        TARGET ReverseTale-CommonServer
        MODULE "ReverseTale/Library:master"
        ENSURE_ORDER
    )
