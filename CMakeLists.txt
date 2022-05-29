cmake_minimum_required(VERSION 3.7...3.23)
if(${CMAKE_VERSION} VERSION_LESS 3.12)
    cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
endif()

# ---- Project ----

project(cpp-weather-roundtrip LANGUAGES CXX)

# ---- Enable FetchContent ----

include(FetchContent)

# ---- Add dependencies via FetchContent ----

# ---- Fetch CPM ----
set(CPM_DOWNLOAD_VERSION 0.35.0)

if(CPM_SOURCE_CACHE)
    set(CPM_DOWNLOAD_LOCATION "${CPM_SOURCE_CACHE}/cpm/CPM_${CPM_DOWNLOAD_VERSION}.cmake")
elseif(DEFINED ENV{CPM_SOURCE_CACHE})
    set(CPM_DOWNLOAD_LOCATION "$ENV{CPM_SOURCE_CACHE}/cpm/CPM_${CPM_DOWNLOAD_VERSION}.cmake")
else()
    set(CPM_DOWNLOAD_LOCATION "${CMAKE_BINARY_DIR}/cmake/CPM_${CPM_DOWNLOAD_VERSION}.cmake")
endif()

if(NOT (EXISTS ${CPM_DOWNLOAD_LOCATION}))
    message(STATUS "Downloading CPM.cmake to ${CPM_DOWNLOAD_LOCATION}")
    file(
            DOWNLOAD
            https://github.com/cpm-cmake/CPM.cmake/releases/download/v${CPM_DOWNLOAD_VERSION}/CPM.cmake
            ${CPM_DOWNLOAD_LOCATION}
    )
endif()

include(${CPM_DOWNLOAD_LOCATION})

# ---- Add dependencies via CPM ----
# See https://github.com/cpm-cmake/CPM.cmake for details and examples

CPMAddPackage(
        NAME doctest
        GITHUB_REPOSITORY sthagen/doctest-doctest
        GIT_TAG 2020.12
)

CPMAddPackage(
        NAME cxxopts
        GITHUB_REPOSITORY jarro2783/cxxopts
        VERSION 3.0.0
        OPTIONS
        "CXXOPTS_BUILD_EXAMPLES Off"
        "CXXOPTS_BUILD_TESTS Off"
)

CPMAddPackage(
        NAME spdlog
        GITHUB_REPOSITORY sthagen/spdlog
        GIT_TAG 2022.3.24
)

CPMAddPackage(
  NAME metaf
  GIT_REPOSITORY https://gitlab.com/sthagen/metaf.git
  VERSION 5.7.1
  # 5.7.1 is not released yet by Nick Naumenko (upstream), so use the latest approved commit
  GIT_TAG 938d57e2acb84431bea1743739327d55492a61f6
)

if(metaf_ADDED)
  add_library(metaf INTERFACE)
  target_include_directories(metaf INTERFACE ${metaf_SOURCE_DIR}/include/)
  message("Added ${metaf_SOURCE_DIR}/include/ as include")
endif()

