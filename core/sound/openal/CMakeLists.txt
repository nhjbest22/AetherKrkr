cmake_minimum_required(VERSION 3.19)
project(aether_openal_device_follower LANGUAGES CXX)

# Follows the system default output for every OpenAL device in the process.
# ALC_SOFT_system_events allows only one event callback per process, so the
# KiriKiri2 renderer and the runtime bridges in the consuming checkout share
# this target instead of registering callbacks of their own.
#
# It depends only on OpenAL, so runtimes that do not require KiriKiri2 can add
# this directory on their own. Guard the add_subdirectory call with
# if(NOT TARGET aether_openal_device_follower).
find_package(OpenAL CONFIG REQUIRED)

# Sanitize vcpkg's raw -Wl,-framework items on this scope's OpenAL copy, as
# the other OpenAL consumers do. The helper is defined by the consuming
# AetherKiri checkout; guard it for other builds.
if(COMMAND aetherkiri_sanitize_openal_link)
    aetherkiri_sanitize_openal_link()
endif()

add_library(aether_openal_device_follower STATIC
    ${CMAKE_CURRENT_LIST_DIR}/OpenALDeviceFollower.cpp
)
target_include_directories(aether_openal_device_follower PUBLIC
    ${CMAKE_CURRENT_LIST_DIR}
)
target_compile_features(aether_openal_device_follower PUBLIC cxx_std_17)
target_link_libraries(aether_openal_device_follower PRIVATE OpenAL::OpenAL)
