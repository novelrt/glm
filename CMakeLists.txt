cmake_minimum_required(VERSION 3.2 FATAL_ERROR)
cmake_policy(VERSION 3.2)


file(READ "glm/detail/setup.hpp" GLM_SETUP_FILE)
string(REGEX MATCH "#define[ ]+GLM_VERSION_MAJOR[ ]+([0-9]+)" _ ${GLM_SETUP_FILE})
set(GLM_VERSION_MAJOR "${CMAKE_MATCH_1}")
string(REGEX MATCH "#define[ ]+GLM_VERSION_MINOR[ ]+([0-9]+)" _ ${GLM_SETUP_FILE})
set(GLM_VERSION_MINOR "${CMAKE_MATCH_1}")
string(REGEX MATCH "#define[ ]+GLM_VERSION_PATCH[ ]+([0-9]+)" _ ${GLM_SETUP_FILE})
set(GLM_VERSION_PATCH "${CMAKE_MATCH_1}")
string(REGEX MATCH "#define[ ]+GLM_VERSION_REVISION[ ]+([0-9]+)" _ ${GLM_SETUP_FILE})
set(GLM_VERSION_REVISION "${CMAKE_MATCH_1}")

set(GLM_VERSION ${GLM_VERSION_MAJOR}.${GLM_VERSION_MINOR}.${GLM_VERSION_PATCH}.${GLM_VERSION_REVISION})
project(glm VERSION ${GLM_VERSION} LANGUAGES CXX)
message(STATUS "GLM: Version " ${GLM_VERSION})

option(GLM_INSTALL "Generate the install target for GLM" ${GLM_ROOT_PROJECT})
option(GLM_BUILD_STATIC_LIBS "Build static library" ${BUILD_STATIC_LIBS})
option(GLM_BUILD_SHARED_LIBS "Build shared library" ${BUILD_SHARED_LIBS})

add_subdirectory(glm)
add_library(glm::glm ALIAS glm)

if(NOT DEFINED GLM_ROOT_PROJECT)
	if(CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
		set(GLM_ROOT_PROJECT ON)
	else()
		set(GLM_ROOT_PROJECT OFF)
	endif()
endif()

if(GLM_INSTALL)

	message(STATUS "Generating GLM installation")
	include(CPack)
	install(DIRECTORY glm DESTINATION ${CMAKE_INSTALL_INCLUDEDIR} PATTERN "CMakeLists.txt" EXCLUDE)
	install(EXPORT glm FILE glmConfig.cmake DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/glm NAMESPACE glm::)
	include(CMakePackageConfigHelpers)
	write_basic_package_version_file("glmConfigVersion.cmake" COMPATIBILITY AnyNewerVersion)
	install(FILES ${CMAKE_CURRENT_BINARY_DIR}/glmConfigVersion.cmake DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/glm)

	if(GLM_BUILD_TESTING)
		include(CTest)
		add_subdirectory(test)
	endif()

endif()

if (NOT TARGET uninstall)
configure_file(cmake/cmake_uninstall.cmake.in
               cmake_uninstall.cmake IMMEDIATE @ONLY)

add_custom_target(uninstall
                  "${CMAKE_COMMAND}" -P
                  "${CMAKE_BINARY_DIR}/cmake_uninstall.cmake")
endif()
