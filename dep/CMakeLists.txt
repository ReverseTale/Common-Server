# Git is required for patches
find_package(Git REQUIRED)

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
        MODULE "mongodb/mongo-c-driver:r1.12"
        CONFIGURE_ARGUMENTS ${MONGOC_CONFIGURE}
    )

    # Libmongoc requires additional steps, it does not follow standard practices...
    AddNonStandardPackage(
        TARGET ReverseTale-CommonServer
        PACKAGE libmongoc-static-1.0
        LIBRARY_VARIABLE MONGOC_STATIC_LIBRARIES
        INCLUDE_VARIABLE MONGOC_STATIC_INDLUDE_DIRS
        DEFINITIONS_VARIABLE MONGOC_STATIC_DEFINITIONS
    )

#######################
# MONGOCXX

    set(MONGOCXX_PATCHES
        "${GIT_EXECUTABLE} stash"
        "${GIT_EXECUTABLE} clean -fx"
        "${GIT_EXECUTABLE} apply --reject --whitespace=fix ${CMAKE_SOURCE_DIR}/cmake/Patches/03-mongocxx-tests.patch"
    )

    set(MONGOCXX_CONFIGURE
        "-DENABLE_TESTS=OFF"     # TODO: No longer can be disabled
        "-DENABLE_EXAMPLES=OFF"  # TODO: No longer can be disabled
        "-DBUILD_SHARED_LIBS=OFF"
    )

    RequireExternal(
        TARGET ReverseTale-CommonServer
        ENSURE_ORDER
        MODULE "mongodb/mongo-cxx-driver:r3.3.1"
        CONFIGURE_STEPS ${MONGOCXX_PATCHES}
        CONFIGURE_ARGUMENTS ${MONGOCXX_CONFIGURE} 
    )

    # Mongocxx requires additional steps, it does not follow standard practices...
    AddNonStandardPackage(
        TARGET ReverseTale-CommonServer
        PACKAGE libmongocxx-static
        LIBRARY_VARIABLE LIBMONGOCXX_STATIC_LIBRARIES
        INCLUDE_VARIABLE LIBMONGOCXX_STATIC_INCLUDE_DIRS
        DEFINITIONS_VARIABLE LIBMONGOCXX_STATIC_DEFINITIONS
    )
    
#######################
# INIH
    set(INIH_PATCHES
        "${GIT_EXECUTABLE} stash"
        "${GIT_EXECUTABLE} clean -fx"
        "${GIT_EXECUTABLE} apply --reject --whitespace=fix ${CMAKE_SOURCE_DIR}/cmake/Patches/01-inih-cmake.patch"
        "${GIT_EXECUTABLE} apply --reject --whitespace=fix ${CMAKE_SOURCE_DIR}/cmake/Patches/02-inih-has_key.patch"
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
