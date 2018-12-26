cmake_minimum_required (VERSION 2.6)
project (SOIL)

option(SOIL_BUILD_TESTS "Build tests" OFF)

if(ANDROID)
  # Android requires GL ES 2.0 package automatically
  set(LIBRARIES GLESv2)
else()
  find_package(OpenGL REQUIRED)
  include_directories(${OPENGL_INCLUDE_DIR})
  set(LIBRARIES ${OPENGL_LIBRARIES})
endif()

file (GLOB SOIL_SOURCES src/*.c)
file (GLOB SOIL_HEADERS src/*.h)

add_library (SOIL ${SOIL_SOURCES})
target_include_directories(SOIL PUBLIC "$<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/src>" $<INSTALL_INTERFACE:include/SOIL>)
target_link_libraries (SOIL ${LIBRARIES})

install(
	TARGETS SOIL
	EXPORT SOILTargets
	LIBRARY DESTINATION "lib"
	ARCHIVE DESTINATION "lib"
	INCLUDES DESTINATION "include/SOIL"
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file("${CMAKE_BINARY_DIR}/SOILConfigVersion.cmake"
  VERSION 1.0.0
  COMPATIBILITY AnyNewerVersion
)

install(
	EXPORT SOILTargets
	FILE SOILTargets.cmake
	NAMESPACE "SOIL::"
	DESTINATION "lib/cmake/SOIL"
)

install(
	FILES
		"${CMAKE_CURRENT_SOURCE_DIR}/SOILConfig.cmake"
		"${CMAKE_BINARY_DIR}/SOILConfigVersion.cmake"
	DESTINATION "lib/cmake/SOIL"
	COMPONENT Devel
)

install(
	FILES ${SOIL_HEADERS}
	DESTINATION "include/SOIL"
)

	
if (SOIL_BUILD_TESTS)
	add_executable (test_SOIL src/test_SOIL.cpp)
	target_link_libraries (test_SOIL SOIL)
	install (TARGETS SOIL test_SOIL RUNTIME DESTINATION bin)
endif ()
