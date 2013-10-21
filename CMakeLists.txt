project(capnproto)
CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
enable_testing()
set(CMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LANGUAGE_STANDARD "c++11")
set(CMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LIBRARY "libc++")

Set(CMAKE_CXX_FLAGS "-std=c++11 -stdlib=libc++")
add_subdirectory(c++)
