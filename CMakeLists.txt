cmake_minimum_required(VERSION 3.2)
project(conanskeleton CXX)

set(CMAKE_CXX_STANDARD 17)

add_compile_options(-Wall -Werror -Wextra)

################################################################################
# conan initialization

# Invoke cmake from a virtualenv with conan and conan-package-tools
# installed.
#
# cmake .. -DCMAKE_CXX_COMPILER=clang++
#          -DCMAKE_EXPORT_COMPILE_COMMANDS=True
#          -DCMAKE_BUILD_TYPE=Debug
#
# The versions at the time were:
# - conan: 1.24.0
# - conan-package-tools: 0.32.2

find_program(CONAN conan)
if (NOT CONAN)
  message(FATAL_ERROR "Please install conan from https://conan.io")
endif()

if(NOT EXISTS "${CMAKE_BINARY_DIR}/conan.cmake")
   message(STATUS "Downloading conan.cmake from https://github.com/conan-io/cmake-conan")
   file(DOWNLOAD "https://raw.githubusercontent.com/conan-io/cmake-conan/master/conan.cmake"
                 "${CMAKE_BINARY_DIR}/conan.cmake")
endif()

include(${CMAKE_BINARY_DIR}/conan.cmake)

# either add remotes here or in your environment with "conan remote add"
conan_add_remote(NAME conan-center URL "https://conan.bintray.com")

# this uses a conan profile conan-clang (optional)
conan_cmake_run(CONANFILE conanfile.txt
  BASIC_SETUP CMAKE_TARGETS
  PROFILE conan-clang
  BUILD missing)

# alternatively (directly specify requirements)
#conan_cmake_run(REQUIRES boost/1.72.0
#                BASIC_SETUP)

################################################################################

add_executable(main main.cpp)
target_link_libraries(main CONAN_PKG::boost)

