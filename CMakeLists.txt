cmake_minimum_required (VERSION 2.6)
project (soil)

if(ANDROID)
  # Android requires GL ES 2.0 package automatically
  set(LIBRARIES GLESv2)
else()
  find_package(OpenGL REQUIRED)
  include_directories(${OPENGL_INCLUDE_DIR})
  set(LIBRARIES ${OPENGL_LIBRARIES})
endif()

list(APPEND LIBRARIES m)

file (GLOB SOURCES src/*.c)
file (GLOB HEADERS include/SOIL/*.h)

add_library (${PROJECT_NAME} ${SOURCES} ${HEADERS})
target_link_libraries (soil PUBLIC ${LIBRARIES})
target_include_directories(soil PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>)

include(GNUInstallDirs)

install(
	TARGETS soil
	EXPORT soilTargets
    LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
    INCLUDES DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
)

install(
	FILES ${HEADERS}
	DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/SOIL"
)

include(CMakePackageConfigHelpers)

write_basic_package_version_file("${CMAKE_BINARY_DIR}/soilConfigVersion.cmake"
  VERSION 1.0.3
  COMPATIBILITY AnyNewerVersion
)

install(
	FILES
		"${CMAKE_CURRENT_SOURCE_DIR}/soilConfig.cmake"
		"${CMAKE_BINARY_DIR}/soilConfigVersion.cmake"
	DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/SOIL"
)

install(
	EXPORT soilTargets
	FILE soilTargets.cmake
	NAMESPACE "soil::"
	DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/SOIL"
)
