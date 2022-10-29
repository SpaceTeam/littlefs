# I don't know the real minimum version. I only have 3.22 installed and it works with that
cmake_minimum_required(VERSION 3.22)

project(
    littlefs
    VERSION 2.5
    DESCRIPTION "A little fail-safe filesystem designed for microcontrollers"
    HOMEPAGE_URL "https://github.com/littlefs-project/littlefs"
    LANGUAGES C
)

add_library(littlefs_littlefs STATIC)
add_library(littlefs::littlefs ALIAS littlefs_littlefs)

target_sources(littlefs_littlefs PRIVATE lfs.c lfs_util.c)
set_target_properties(littlefs_littlefs PROPERTIES OUTPUT_NAME littlefs EXPORT_NAME littlefs)

# Install with CMake:
# cmake [--toolchain <toolchain file> [other options]] -S . -B build
# cmake --build build
# sudo cmake --install build [--prefix <prefix of install directory>]

# Use in other project with CMake:
# find_package(littlefs [<version>] REQUIRED)
# target_link_libraries(<target> (PUBLIC|PRIVATE) littlefs::littlefs)

# Install rules

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

set(package littlefs)
set(littlefs_INSTALL_CMAKEDIR ${CMAKE_INSTALL_LIBDIR}/cmake/${package})

# install( DIRECTORY ${CMAKE_SOURCE_DIR}/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${package}
# FILES_MATCHING PATTERN "*.h" )
install(FILES lfs.h lfs_util.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${package})
target_include_directories(
    littlefs_littlefs SYSTEM INTERFACE $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

write_basic_package_version_file(
    "${package}ConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

install(
    FILES install-config.cmake
    DESTINATION ${littlefs_INSTALL_CMAKEDIR}
    RENAME "${package}Config.cmake"
)
install(FILES "${PROJECT_BINARY_DIR}/${package}ConfigVersion.cmake"
        DESTINATION ${littlefs_INSTALL_CMAKEDIR}
)

install(
    TARGETS littlefs_littlefs
    EXPORT littlefsTargets
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}/${package}
)
install(
    EXPORT littlefsTargets
    NAMESPACE littlefs::
    DESTINATION ${littlefs_INSTALL_CMAKEDIR}
)
