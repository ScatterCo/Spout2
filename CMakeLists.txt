cmake_minimum_required(VERSION 3.4)

project(Spout2)

set(LIB_SRC_DIR "${CMAKE_CURRENT_SOURCE_DIR}/SpoutSDK/Source")

file(GLOB LIB_SOURCES
	"${LIB_SRC_DIR}/*.cpp"
)

file(GLOB LIB_HEADERS
	"${LIB_SRC_DIR}/*.h"
)

option(SPOUT2_BUILD_STATIC "build static version of lib" ON)


if(SPOUT2_BUILD_STATIC)
	add_library(${PROJECT_NAME} STATIC ${LIB_SOURCES} ${LIB_HEADERS})
else()
  add_definitions(SPOUT_BUILD_DLL SPOUTSDK_EXPORTS)
	add_library(${PROJECT_NAME} SHARED ${LIB_SOURCES} ${LIB_HEADERS})
endif()

target_link_libraries(${PROJECT_NAME} opengl32)

target_include_directories(${PROJECT_NAME} INTERFACE $<BUILD_INTERFACE:${LIB_SRC_DIR}>
                                                   $<INSTALL_INTERFACE:include>)

set_target_properties(${PROJECT_NAME} PROPERTIES PUBLIC_HEADER "${LIB_SRC_DIR}/SpoutSDK.h")
set_target_properties(${PROJECT_NAME} PROPERTIES DEBUG_POSTFIX "d")

install(
    TARGETS ${PROJECT_NAME}
    EXPORT ${PROJECT_NAME}targets
    LIBRARY DESTINATION "${CMAKE_INSTALL_PREFIX}/lib"
    ARCHIVE DESTINATION "${CMAKE_INSTALL_PREFIX}/lib/static"
    RUNTIME DESTINATION "${CMAKE_INSTALL_PREFIX}/bin"
    PUBLIC_HEADER DESTINATION "${CMAKE_INSTALL_PREFIX}/include"
)

include(CMakePackageConfigHelpers)
configure_package_config_file("${CMAKE_CURRENT_LIST_DIR}/cmake/${PROJECT_NAME}Config.cmake.in"
                              "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
                              INSTALL_DESTINATION "${CMAKE_INSTALL_PREFIX}/lib/cmake")

install(
	FILES "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
	DESTINATION "${CMAKE_INSTALL_PREFIX}/lib/cmake"
)

install(
  EXPORT ${PROJECT_NAME}targets DESTINATION "${CMAKE_INSTALL_PREFIX}/lib/cmake"
)
