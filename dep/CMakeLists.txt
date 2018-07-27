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

    # Libbson requires additional steps, it does not follow standard practices...
    AddNonStandardPackage(
        TARGET ReverseTale-CommonServer
        PACKAGE libbson-static-1.0
        LIBRARY_VARIABLE BSON_STATIC_LIBRARY
        INCLUDE_VARIABLE BSON_STATIC_INDLUDE_DIRS
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

    # Libmongoc requires additional steps, it does not follow standard practices...
    AddNonStandardPackage(
        TARGET ReverseTale-CommonServer
        PACKAGE libmongoc-static-1.0
        LIBRARY_VARIABLE MONGOC_STATIC_LIBRARY
        INCLUDE_VARIABLE MONGOC_STATIC_INDLUDE_DIRS
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

    # Mongocxx requires additional steps, it does not follow standard practices...
    AddNonStandardPackage(
        TARGET ReverseTale-CommonServer
        PACKAGE libmongocxx-3.2.0
        CONFIG libmongocxx
        LIBRARY_VARIABLE LIBMONGOCXX_LIBRARIES
        INCLUDE_VARIABLE LIBMONGOCXX_INCLUDE_DIRS
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
        INSTALL_NAME inih
    )

#######################
# THREADPOOL11

    RequireExternal(
        TARGET ReverseTale-CommonServer
        MODULE "gpascualg/threadpool11:2.0"
        INSTALL_NAME threadpool11
    )

#######################
# ReverseTale-Library

    RequireExternal(
        TARGET ReverseTale-CommonServer
        MODULE "ReverseTale/Library:master"
        ENSURE_ORDER
        INSTALL_NAME ReverseTale-Library
    )
