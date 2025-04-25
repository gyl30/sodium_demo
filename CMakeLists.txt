cmake_minimum_required(VERSION 3.15)
project(sodium)

# 设置 C++17
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# 构建类型和调试信息
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Debug)
  # 编译器警告和调试选项
  add_compile_options(-Wall -fno-strict-aliasing -fno-omit-frame-pointer -g -O0 -ggdb)
endif()

# 设置 vcpkg 工具链
if(DEFINED ENV{VCPKG_INSTALLATION_ROOT})
    set(CMAKE_TOOLCHAIN_FILE "$ENV{VCPKG_INSTALLATION_ROOT}/scripts/buildsystems/vcpkg.cmake")
    message(STATUS "Using VCPKG Toolchain from: ${CMAKE_TOOLCHAIN_FILE}")
elseif(DEFINED ENV{VCPKG_ROOT})
    set(CMAKE_TOOLCHAIN_FILE "$ENV{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake")
    message(STATUS "Using VCPKG Toolchain from: ${CMAKE_TOOLCHAIN_FILE}")
else()
    message(WARNING "VCPKG_ROOT or VCPKG_INSTALLATION_ROOT not defined, vcpkg integration may fail")
endif()

# ================= Sodium + libuv =================
if(WIN32)
  find_package(unofficial-sodium CONFIG REQUIRED)
  list(APPEND LINK_LIBS 
    unofficial-sodium::sodium
    ws2_32
  )
else()
  find_package(PkgConfig REQUIRED)
  pkg_check_modules(libsodium REQUIRED IMPORTED_TARGET libsodium)
  list(APPEND LINK_LIBS PkgConfig::libsodium)
endif()

add_executable(sodium main.cc)

target_link_libraries(sodium ${LINK_LIBS})

