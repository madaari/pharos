# In order to make release or debug builds, set CMAKE_BUILD_TYPE to Release or Debug.
# In order to change the install prefix, set CMAKE_INSTALL_PREFIX.

# User customizations can be placed in this directory in a file named "local.cmake".

cmake_minimum_required(VERSION 3.5)

# <Package>_ROOT variables are now added to the path by default.  We
# were explicitly using our _ROOT variables for this purpose, so the
# "NEW" policy should be fine, although we might want to revisit
# whether we should be doing things differently under the new policy.
if(POLICY CMP0074)
  cmake_policy(SET CMP0074 NEW)
endif()

project(pharos CXX C)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
  "Debug" "Release" "RelWithDebInfo" "MinSizeRel")

include(AddCXXCompilerFlags)
add_cxx_compiler_flags(-Wall -Wextra -Wshadow -Wstrict-aliasing -Wno-misleading-indentation "-ftemplate-depth=1024")

set(CMAKE_INSTALL_RPATH_USE_LINK_PATH true)

# Load the user's customizations, then the site customization, then
# the local customization.  This lets the local customization take
# precedence over site when setting cache variables, and when setting
# non-cache variables.
include("${CMAKE_CURRENT_SOURCE_DIR}/local.cmake" OPTIONAL)
include("${CMAKE_CURRENT_SOURCE_DIR}/site.cmake" OPTIONAL)
include("${CMAKE_CURRENT_SOURCE_DIR}/local.cmake" OPTIONAL)

set(ROSE_ROOT  "" CACHE PATH "The root directory of the ROSE library")
set(BOOST_ROOT "" CACHE PATH "The root directory of the Boost installation")
set(Z3_ROOT  "" CACHE PATH "The root directory of the Z3 installation")
set(YAML_CPP_ROOT "" CACHE PATH "The root directory of the yaml-cpp installation")

include("${CMAKE_CURRENT_SOURCE_DIR}/site.cmake" OPTIONAL)
include("${CMAKE_CURRENT_SOURCE_DIR}/local.cmake" OPTIONAL)

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()

message(STATUS "Using Build Type: ${CMAKE_BUILD_TYPE}")

set(CMAKE_CXX_EXTENSIONS off)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED on)
set(THREADS_PREFER_PTHREAD_FLAG on)
if(CMAKE_BUILD_TYPE STREQUAL "Release")
  set(PHAROS_RELEASE true)
endif()

# Rose
find_package(Rose REQUIRED)

# Find git
find_package(Git REQUIRED)

# Global testing parameters
enable_testing()
add_subdirectory(tests)
set(PHAROS_TEST_OPS --no-site-file --no-user-file ${TESTCONFIG_SWITCH})
set(CTEST_TEST_TIMEOUT 300)     # 5 minutes

# Add pod building
include(BuildPharosPod)

# Individual tests (or groups of tests should add themselves as dependencies).
if (PHAROS_BUILD_TESTS)
  add_custom_target(tests ALL)
else()
  add_custom_target(tests)
endif()

# Add directories
add_subdirectory(libpharos)
add_subdirectory(tools)
add_subdirectory(gtest)
add_subdirectory(share)

install(FILES README.md CONTRIBUTING.md LICENSE.md INSTALL.md DESTINATION share/doc/pharos
  PERMISSIONS OWNER_READ OWNER_WRITE GROUP_READ WORLD_READ)
