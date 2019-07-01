cmake_minimum_required(VERSION 3.1)

get_filename_component(PROJECT_NAME ${CMAKE_CURRENT_SOURCE_DIR} NAME)
project(${PROJECT_NAME})

# One shouldn't generate the BUILD project directly in the SOURCES folder!
if ( ${CMAKE_CURRENT_BINARY_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR} )
        if ( NOT SAME_BUILD_AND_SOURCE_FOLDER_WARNING_ALREADY_ISSUED )
                message(FATAL_ERROR "It is not advised to BUILD the binaries directly in the SOURCE folder!\n If you want to proceed with this option, just CONFIGURE the project once again" )
                set( SAME_BUILD_AND_SOURCE_FOLDER_WARNING_ALREADY_ISSUED TRUE )
        endif()
endif()

################################################################################

set (CMAKE_CXX_STANDARD 11)

################################################################################

# Detects whether this is a top-level project
if(CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)
	set(EASY3D_TOPLEVEL_PROJECT ON)
else()
	set(EASY3D_TOPLEVEL_PROJECT OFF)
endif()

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# Build tutorials
option(EASY3D_BUILD_TUTORIALS    "Build Easy3D Tutorials"     ON)

################################################################################

### Configuration
set(EASY3D_ROOT         ${CMAKE_CURRENT_LIST_DIR})
set(EASY3D_INCLUDE_DIR  ${EASY3D_ROOT} ${CMAKE_CURRENT_BINARY_DIR})
set(EASY3D_SOURCE_DIR   ${EASY3D_ROOT})


set(EASY3D_EXTERNAL             ${EASY3D_ROOT}/3rd_party)
set(EASY3D_glew_INCLUDE_DIR     ${EASY3D_EXTERNAL}/glew/include)
set(EASY3D_glfw_INCLUDE_DIR     ${EASY3D_EXTERNAL}/glfw/include)
set(EASY3D_imgui_INCLUDE_DIR    ${EASY3D_EXTERNAL}/imgui ${EASY3D_EXTERNAL}/imgui/impl)
set(EASY3D_lastools_INCLUDE_DIR ${EASY3D_EXTERNAL}/LAStools/LASzip/src ${EASY3D_EXTERNAL}/LAStools/LASlib/inc)
set(EASY3D_rply_INCLUDE_DIR     ${EASY3D_EXTERNAL}/rply)
set(EASY3D_kdtree_INCLUDE_DIR   ${EASY3D_EXTERNAL}/kd_tree)
set(EASY3D_ransac_INCLUDE_DIR   ${EASY3D_EXTERNAL}/RANSAC-1.1)
set(EASY3D_poisson_INCLUDE_DIR  ${EASY3D_EXTERNAL}/poisson_recon-9.0.1)
set(EASY3D_tinyfiledialogs_INCLUDE_DIR  ${EASY3D_EXTERNAL}/tinyfiledialogs)
set(EASY3D_opcode_INCLUDE_DIR   ${EASY3D_EXTERNAL}/Opcode-1.3)

set(EASY3D_glog_INCLUDE_DIR     ${EASY3D_EXTERNAL}/glog/src ${CMAKE_CURRENT_BINARY_DIR}/3rd_party/glog)

### conditionally compile certain modules depending on libraries found on the system
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_LIST_DIR}/cmake)

################################################################################

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

################################################################################

add_subdirectory(3rd_party)

add_subdirectory(easy3d)

if(EASY3D_BUILD_TUTORIALS)
    add_subdirectory(tutorials)
endif()


## Resources
# Copy resources dirs into our shadow build directory
file( COPY ${CMAKE_CURRENT_SOURCE_DIR}/resource DESTINATION ${CMAKE_BINARY_DIR} )


# cmake generating deb - based on 
# https://nowardev.wordpress.com/2009/12/27/how-to-create-a-debian-package-for-script-and-for-project-that-use-cmake/
SET(MAJOR_VERSION 1)
SET(MINOR_VERSION 0)
SET(PATCH_VERSION 0)
 
IF(EXISTS "${CMAKE_ROOT}/Modules/CPack.cmake")
INCLUDE(InstallRequiredSystemLibraries)
 
SET(CPACK_SET_DESTDIR "on")
SET(CPACK_PACKAGING_INSTALL_PREFIX "/tmp")
SET(CPACK_GENERATOR "DEB")
 
SET(CPACK_PACKAGE_DESCRIPTION "short description")
SET(CPACK_PACKAGE_DESCRIPTION_SUMMARY "long description bla bla bla... bah")
SET(CPACK_PACKAGE_VENDOR "Vendor")
SET(CPACK_PACKAGE_CONTACT "developer ")
SET(CPACK_PACKAGE_VERSION_MAJOR "${MAJOR_VERSION}")
SET(CPACK_PACKAGE_VERSION_MINOR "${MINOR_VERSION}")
SET(CPACK_PACKAGE_VERSION_PATCH "${PATCH_VERSION}")
SET(CPACK_PACKAGE_FILE_NAME "${CMAKE_PROJECT_NAME}_${MAJOR_VERSION}.${MINOR_VERSION}.${CPACK_PACKAGE_VERSION_PATCH}")
SET(CPACK_SOURCE_PACKAGE_FILE_NAME "${CMAKE_PROJECT_NAME}_${MAJOR_VERSION}.${MINOR_VERSION}.${CPACK_PACKAGE_VERSION_PATCH}")
 
#SET(CPACK_DEBIAN_PACKAGE_DEPENDS " libqt4-dbus (>= 4.5.0), libqtcore4 (>= 4.5.0), libqtgui4 (>= 4.5.0), libstdc++6 (>= 4.2.1), libglu1-mesa-dev, xorg-dev, x11-apps")
SET(CPACK_DEBIAN_PACKAGE_DEPENDS "libglu1-mesa-dev, xorg-dev, x11-apps")
 
SET(CPACK_DEBIAN_PACKAGE_PRIORITY "optional")
SET(CPACK_DEBIAN_PACKAGE_SECTION "devel")
SET(CPACK_DEBIAN_ARCHITECTURE ${CMAKE_SYSTEM_PROCESSOR})
 
SET(CPACK_COMPONENTS_ALL Libraries ApplicationData)
INCLUDE(CPack)
 
ENDIF(EXISTS "${CMAKE_ROOT}/Modules/CPack.cmake")
