cmake_minimum_required( VERSION 3.10 FATAL_ERROR )

project( kicad-templates NONE )

#================================================
# Locations for install targets.
#================================================
if( APPLE )
    # Like all variables, CMAKE_INSTALL_PREFIX can be over-ridden on the command line.
    set( CMAKE_INSTALL_PREFIX "/Library/Application Support/kicad/" CACHE PATH "" )
    # Everything without leading / is relative to CMAKE_INSTALL_PREFIX.
    set( KICAD_TEMPLATE template )
else()
    if( CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT )
        set( CMAKE_INSTALL_PREFIX "/home/amarjay/kicad" CACHE PATH "Default install path" FORCE )
    endif()

    # Everything without leading / is relative to CMAKE_INSTALL_PREFIX.
		# Find the highest KiCad version installed
		# set(KICAD_BASE "/usr/share/kicad")
		# file(GLOB VERSION_DIRS LIST_DIRECTORIES true "${KICAD_BASE}/*")
		# set(KICAD_VERSION "9.0") # default
		# foreach(dir ${VERSION_DIRS})
		# 	get_filename_component(ver ${dir} NAME)
		# 	if(ver MATCHES "^[0-9]+\\.[0-9]+$" AND ver VERSION_GREATER KICAD_VERSION)
		# 		set(KICAD_VERSION ${ver})
		# 	endif()
		# endforeach()
		set( KICAD_VERSION "9.0" ) # Set default to KiCad 9.0
		set( KICAD_DATA ${KICAD_VERSION}
			CACHE PATH "Location of KiCad data files." )
    set( KICAD_TEMPLATE ${KICAD_DATA}/template )
endif()

# mark_as_advanced( KICAD_DATA )


#================================================
# "make uninstall" rules
#================================================
configure_file(
    "${PROJECT_SOURCE_DIR}/CMakeModules/cmake_uninstall.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
    IMMEDIATE @ONLY
    )

add_custom_target( uninstall
    "${CMAKE_COMMAND}" -P "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
    )


#================================================
# Installed files.
#================================================

# Select project template directories
file (GLOB PROJECT_DIRS "Projects/*")

# Copy them to destination
foreach( tpl ${PROJECT_DIRS} )
    install( DIRECTORY ${tpl} DESTINATION ${KICAD_TEMPLATE} COMPONENT resources )
endforeach()

# The supplied worksheet templates
file( GLOB_RECURSE kicad_wks_files "Worksheets/*.kicad_wks" )

install( FILES ${kicad_wks_files}
    DESTINATION ${KICAD_TEMPLATE}
    COMPONENT resources
    )
