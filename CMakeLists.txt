# ---[ Create project
project(ReverseTale-CommonServer)

# ---[ Parse all prefixes
include(cmake-common/Utils.cmake)
String2List(${CMAKE_PREFIX_PATH} CMAKE_PREFIX_PATH)
String2List(${CMAKE_MODULE_PATH} CMAKE_MODULE_PATH)

# ---[ Find all source and header files
file(GLOB_RECURSE sources_Main_H    src/*.h)
file(GLOB_RECURSE sources_Main_CPP  src/*.cpp)
set(CommonServer_SOURCES
    ${sources_Main_H}
    ${sources_Main_CPP}
)

# ---[ Load dependencies
find_package(LibBSON                REQUIRED)
find_package(LibMongoC              REQUIRED)
find_package(libbsoncxx             REQUIRED)
find_package(libmongocxx            REQUIRED)
find_package(inih                   REQUIRED)
find_package(threadpool11           REQUIRED)

if (${ReverseTale_DEV})
    include_directories(${CMAKE_SOURCE_DIR}/src/Library/include)
else()
    find_package(ReverseTale-Library    REQUIRED)
endif()

# ---[ Setup include directories
include_directories(
    ${CMAKE_BINARY_DIR}
    ${CMAKE_SOURCE_DIR}/src
)

# ---[ Setup some definitions
set(DEFINITIONS "MONGOCXX_STATIC=1 BSONCXX_STATIC=1")
#if (NOT ${DEBUG})
#   set(DEFINITIONS "${DEFINITIONS} NDEBUG=1")
#endif()
add_definitions(-DMONGOCXX_STATIC -DBSONCXX_STATIC)

# ---[ Add library as static and add dependencies
add_library(ReverseTale-CommonServer STATIC ${CommonServer_SOURCES})

# ---[ Find absolute paths to libraries
find_library(LIBBSON_LIBRARY NAMES bson-static-1.0.lib ${LIBBSON_LIBRARIES} PATHS ${LIBBSON_LIBRARY_DIRS})
find_library(LIBMONGOC_LIBRARY NAMES mongoc-static-1.0.lib ${LIBMONGOC_LIBRARIES} PATHS ${LIBMONGOC_LIBRARY_DIRS})
find_library(LIBBSONCXX_LIBRARY NAMES libmongocxx.lib ${LIBBSONCXX_LIBRARIES} PATHS ${LIBBSONCXX_LIBRARY_DIRS})
find_library(LIBMONGOCXX_LIBRARY NAMES libbsoncxx.lib ${LIBMONGOCXX_LIBRARIES} PATHS ${LIBMONGOCXX_LIBRARY_DIRS})

# ---[ Link all third party libraries
target_link_libraries(ReverseTale-CommonServer
    PUBLIC ReverseTale-Library
    PUBLIC ${LIBBSON_LIBRARY} # Not needed, libbsoncxx already links it
    PUBLIC ${LIBMONGOC_LIBRARY} # Not needed, libmongocxx already links it
    PUBLIC ${LIBBSONCXX_LIBRARY}
    PUBLIC ${LIBMONGOCXX_LIBRARY}
    PUBLIC "$<TARGET_FILE:inih>"
    PUBLIC "$<TARGET_FILE:threadpool11>"
)

get_target_property(INIH_INCLUDE_DIR inih INTERFACE_INCLUDE_DIRECTORIES)
get_target_property(THREADPOOL11_INCLUDE_DIR threadpool11 INTERFACE_INCLUDE_DIRECTORIES)

target_include_directories(ReverseTale-CommonServer
	#PUBLIC "$<TARGET_PROPERTY:ReverseTale-Library,INTERFACE_INCLUDE_DIRECTORIES>"
	PUBLIC ${LibBSON_INCLUDE_DIRS}
    PUBLIC ${LibMongoC_INCLUDE_DIRS}
    PUBLIC ${LIBBSONCXX_INCLUDE_DIRS}
    PUBLIC ${LIBMONGOCXX_INCLUDE_DIRS}
    PUBLIC ${INIH_INCLUDE_DIR}
    PUBLIC ${THREADPOOL11_INCLUDE_DIR}
    INTERFACE "$<INSTALL_INTERFACE:include>"
)

# ---[ Set C++11 standard
set_property(TARGET ReverseTale-CommonServer PROPERTY CXX_STANDARD 11)
target_compile_features(ReverseTale-CommonServer PRIVATE cxx_decltype cxx_nullptr cxx_variadic_templates)

# ---[ Setup output directory and include directories
set_target_properties(ReverseTale-CommonServer PROPERTIES
    OUTPUT_NAME CommonServer
    ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/../lib
    ARCHIVE_OUTPUT_DIRECTORY_DEBUG ${PROJECT_SOURCE_DIR}/../lib
    ARCHIVE_OUTPUT_DIRECTORY_RELEASE ${PROJECT_SOURCE_DIR}/../lib

    INTERFACE_COMPILE_DEFINITIONS "${DEFINITIONS}"
)

include(CMakePackageConfigHelpers)
set(config_install_dir lib/cmake/reversetale-commonserver)
set(version_config ${PROJECT_BINARY_DIR}/reversetale-commonserver-config-version.cmake)
set(project_config ${PROJECT_BINARY_DIR}/reversetale-commonserver-config.cmake)
set(targets_export_name reversetale-commonserver-targets)


# Generate the version, config and target files into the build directory.
write_basic_package_version_file(
  ${version_config}
  VERSION 0.0.1
  COMPATIBILITY AnyNewerVersion)
configure_package_config_file(
  ${PROJECT_SOURCE_DIR}/config.cmake.in
  ${project_config}
  INSTALL_DESTINATION ${config_install_dir})
export(TARGETS ReverseTale-CommonServer FILE ${PROJECT_BINARY_DIR}/${targets_export_name}.cmake)

install(TARGETS ReverseTale-CommonServer
	EXPORT ${targets_export_name}
	RUNTIME DESTINATION bin/
  	LIBRARY DESTINATION lib/
  	ARCHIVE DESTINATION lib/)

install(FILES ${project_config} ${version_config} DESTINATION ${config_install_dir})
install(EXPORT ${targets_export_name} DESTINATION ${config_install_dir})
install(FILES ${sources_Main_H} DESTINATION include/)
