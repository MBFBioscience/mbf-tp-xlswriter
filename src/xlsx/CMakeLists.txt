
################################################################################
# Qt deps
################################################################################
find_package(Qt6 COMPONENTS Widgets REQUIRED)

set(CMAKE_AUTOMOC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

include_directories(${Qt6Widgets_INCLUDE_DIRS})
include_directories(${Qt6Widgets_PRIVATE_INCLUDE_DIRS})
message(STATUS "QT INCLUDE DIRS : ${Qt6Widgets_INCLUDE_DIRS}")
message(STATUS "QT PRIVATE INCLUDE DIRS : ${Qt6Widgets_PRIVATE_INCLUDE_DIRS}")

################################################################################
# Preprocessor definitions for building xlsxWriter
################################################################################
add_definitions(-DXLSX_NO_LIB)

################################################################################
# Build options
################################################################################
if (MSVC)
    add_compile_options(/MP)
    add_definitions(-DUNICODE -D_UNICODE) 
else ()
    set(CMAKE_CXX_STANDARD 11)
endif()

################################################################################
# Source files
################################################################################
file (GLOB_RECURSE XLSXWRITER_FILES_SRC
  RELATIVE ${CMAKE_CURRENT_SOURCE_DIR}
  *.cpp)
list(REMOVE_ITEM XLSXWRITER_FILES_SRC
  xlsxwriter.cpp)

file (GLOB_RECURSE XLSXWRITER_FILES_HDR
  RELATIVE ${CMAKE_CURRENT_SOURCE_DIR}
  *.h)
list(REMOVE_ITEM XLSXWRITER_FILES_HDR
  xlsxwriter.h)

################################################################################
# Link libraries
################################################################################
add_library(xlsxWriter STATIC ${XLSXWRITER_FILES_SRC})

################################################################################
# Header file set
################################################################################
target_sources(${PROJECT_NAME} PUBLIC
	FILE_SET public_headers
	TYPE HEADERS
	FILES ${XLSXWRITER_FILES_HDR}
)

################################################################################
# Build include directories
################################################################################
target_include_directories(${PROJECT_NAME} PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}")

################################################################################
# Install target
################################################################################
install(TARGETS ${PROJECT_NAME}
		EXPORT ${PROJECT_NAME}-targets
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
		FILE_SET public_headers DESTINATION "include/xlswriter"
)

################################################################################
# Export and install cmake configuration files
################################################################################
export(TARGETS ${PROJECT_NAME}
    NAMESPACE ${PROJECT_NAME}::
    FILE "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
)

install(EXPORT ${PROJECT_NAME}-targets
	FILE "${PROJECT_NAME}Config.cmake"
	NAMESPACE ${PROJECT_NAME}::
    DESTINATION "share/${PROJECT_NAME}"
)
