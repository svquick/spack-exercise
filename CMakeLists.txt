cmake_minimum_required(VERSION "3.12")
project("spackexample" VERSION 0.3.0)

option(WITH_BOOST "Enable Boost" ON)
option(WITH_YAML "Enable yaml-cpp" ON)

if(WITH_BOOST)
  find_package(Boost 1.65.1 REQUIRED filesystem)
endif()

if(WITH_YAML)
  find_package(yaml-cpp 0.7.0 REQUIRED)
endif()

set(SOURCES
    filesystem/filesystem.cpp
    flatset/flatset.cpp
)

if(WITH_YAML)
    list(APPEND SOURCES yamlParser/yamlParser.cpp)
endif()

add_library(spackexamplelib ${SOURCES})

set_target_properties(spackexamplelib
  PROPERTIES
    PUBLIC_HEADER filesystem/filesystem.hpp
    PUBLIC_HEADER flatset/flatset.hpp
)

add_executable(spackexample main.cpp)
target_link_libraries(spackexample spackexamplelib)

target_include_directories(spackexamplelib
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/spackexamplelib
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/spackexamplelib>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/spackexamplelib>
)

if(WITH_BOOST)
    target_link_libraries(spackexamplelib Boost::filesystem)
endif()

if(WITH_YAML)
    target_include_directories(spackexamplelib PUBLIC ${YAML_CPP_INCLUDE_DIR})
    target_link_libraries(spackexamplelib yaml-cpp)
endif()

include(GNUInstallDirs)
install(TARGETS spackexample spackexamplelib
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/spackexample
  INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/spackexample
)