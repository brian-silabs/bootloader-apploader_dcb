# Define minimal required version of CMake.
cmake_minimum_required(VERSION "3.25")

# Project definition
project(
	bootloader-apploader_dcb
	VERSION 1.0
	LANGUAGES C CXX ASM
)

# Include the definition of the slc_bootloader-apploader_dcb target,
# which contains the content of the SLC project
include(bootloader-apploader_dcb.cmake)

add_executable(bootloader-apploader_dcb
    # Add additional sources here
)

target_include_directories(bootloader-apploader_dcb PUBLIC
    # Add additional include paths here
)

target_compile_definitions(bootloader-apploader_dcb PUBLIC
    # Add additional macros here
)

target_compile_options(bootloader-apploader_dcb PUBLIC
    # Set additional compiler flags here
)

target_link_options(bootloader-apploader_dcb PUBLIC
    # Set additional linker flags here
)

# Link with the content defined in the SLC project
target_link_libraries(bootloader-apploader_dcb PRIVATE
    slc_bootloader-apploader_dcb
)

# Include managed project content if available
include(bootloader-apploader_dcb_project.cmake OPTIONAL RESULT_VARIABLE managed_project)
if(managed_project)
    message(STATUS "Using managed project content from ${managed_project}")
endif()

# Force the gcc linker command
set_target_properties(bootloader-apploader_dcb PROPERTIES LINKER_LANGUAGE C)

# Create .bin, .hex and .s37 artifacts after building the project
add_custom_command(TARGET bootloader-apploader_dcb
    POST_BUILD
    COMMAND ${CMAKE_OBJCOPY} ${OBJCOPY_SREC_CMD} "$<TARGET_FILE:bootloader-apploader_dcb>" "$<TARGET_FILE_DIR:bootloader-apploader_dcb>/$<TARGET_FILE_BASE_NAME:bootloader-apploader_dcb>.s37"
    COMMAND ${CMAKE_OBJCOPY} ${OBJCOPY_IHEX_CMD} "$<TARGET_FILE:bootloader-apploader_dcb>" "$<TARGET_FILE_DIR:bootloader-apploader_dcb>/$<TARGET_FILE_BASE_NAME:bootloader-apploader_dcb>.hex"
    COMMAND ${CMAKE_OBJCOPY} ${OBJCOPY_BIN_CMD}  "$<TARGET_FILE:bootloader-apploader_dcb>" "$<TARGET_FILE_DIR:bootloader-apploader_dcb>/$<TARGET_FILE_BASE_NAME:bootloader-apploader_dcb>.bin" 
)

# Run post-build pipeline to perform additional post-processing
if(post_build_command)
add_custom_command(TARGET bootloader-apploader_dcb
    POST_BUILD
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/..
    COMMAND ${post_build_command}
)
endif()