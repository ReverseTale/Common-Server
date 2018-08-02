# ---[ Create project
cmake_minimum_required(VERSION 2.8.0)
cmake_policy(SET CMP0022 NEW)
project(ReverseTale-CommonServer)

# ---[ Includes
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake-utils)

# -[ Macros and functions
include(Configure)
include(Macros)
include(External)
ResetAllTargets()

# ---[ Create project
CreateTarget(TARGET ReverseTale-CommonServer)

# ---[ Find all source and header files
AddToSources(
    TARGET ReverseTale-CommonServer
    SRC_PATH "src"
    GLOB_SEARCH ".hpp;.cpp"
    FOLDER_NAME "src"
)

# ---[ Load dependencies
add_subdirectory(dep)

ResolveExternal(TARGET ReverseTale-CommonServer)
if (ReverseTale-CommonServer_IS_RESOLVED)
	# ---[ Setup include directories
	AddToIncludes(
		TARGET ReverseTale-CommonServer
		INC_PATH ${CMAKE_CURRENT_SOURCE_DIR}/include
	)

	BuildNow(
		TARGET ReverseTale-CommonServer
		STATIC_LIB
		OUTPUT_NAME CommonServer
		C++11
	)

    MakeInstallable(TARGET ReverseTale-CommonServer)
endif()
