cmake_minimum_required(VERSION 3.21.1)

include(CMakeDependentOption)
include(CheckIncludeFile)

project(csbug LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)

add_executable(astandalone standalone.cc)
add_executable(BundleExec MACOSX_BUNDLE BundleExec.cc)

find_package(Qt6 REQUIRED COMPONENTS Core)
target_link_libraries(BundleExec PRIVATE Qt6::Core)

get_target_property(_macdeployqt_executable Qt6::macdeployqt IMPORTED_LOCATION)
message(STATUS "Using Qt6 ${_macdeployqt_executable}")

add_custom_target(package_app
                   COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:astandalone> $<TARGET_BUNDLE_CONTENT_DIR:BundleExec>/MacOS/
                   COMMAND ${_macdeployqt_executable} $<TARGET_BUNDLE_DIR:BundleExec> -verbose=4 -codesign=-
                   DEPENDS astandalone BundleExec
                   VERBATIM
                   USES_TERMINAL
                  )

