# install CCOLAMD headers
install(FILES CCOLAMD/Include/ccolamd.h DESTINATION include/gtsam/3rdparty/CCOLAMD)
install(FILES UFconfig/UFconfig.h DESTINATION include/gtsam/3rdparty/UFconfig)

if(NOT GTSAM_USE_SYSTEM_EIGEN)
	# Find plain .h files
	file(GLOB_RECURSE eigen_headers "${CMAKE_CURRENT_SOURCE_DIR}/Eigen/Eigen/*.h")

	# Find header files without extension
	file(GLOB eigen_dir_headers_all "Eigen/Eigen/*")
	foreach(eigen_dir ${eigen_dir_headers_all})
		get_filename_component(filename ${eigen_dir} NAME)
		if (NOT ((${filename} MATCHES "CMakeLists.txt") OR (${filename} MATCHES "src") OR (${filename} MATCHES ".svn")))
			list(APPEND eigen_headers "${CMAKE_CURRENT_SOURCE_DIR}/Eigen/Eigen/${filename}")
			install(FILES Eigen/Eigen/${filename} DESTINATION include/gtsam/3rdparty/Eigen/Eigen)
		endif()
	endforeach(eigen_dir)

	# do the same for the unsupported eigen folder
	file(GLOB_RECURSE  unsupported_eigen_headers "${CMAKE_CURRENT_SOURCE_DIR}/Eigen/unsupported/Eigen/*.h")
	
	file(GLOB unsupported_eigen_dir_headers_all "Eigen/unsupported/Eigen/*")
	foreach(unsupported_eigen_dir ${unsupported_eigen_dir_headers_all})
		get_filename_component(filename ${unsupported_eigen_dir} NAME)
		if (NOT ((${filename} MATCHES "CMakeLists.txt") OR (${filename} MATCHES "src") OR (${filename} MATCHES ".svn")))
			list(APPEND unsupported_eigen_headers "${CMAKE_CURRENT_SOURCE_DIR}/Eigen/unsupported/Eigen/${filename}")
			install(FILES Eigen/unsupported/Eigen/${filename} DESTINATION include/gtsam/3rdparty/Eigen/unsupported/Eigen)
		endif()
	endforeach(unsupported_eigen_dir)

	# Add to project source
	set(eigen_headers ${eigen_headers} PARENT_SCOPE)
	# set(unsupported_eigen_headers ${unsupported_eigen_headers} PARENT_SCOPE)

	# install Eigen - only the headers in our 3rdparty directory
	install(DIRECTORY Eigen/Eigen
		DESTINATION include/gtsam/3rdparty/Eigen
		FILES_MATCHING PATTERN "*.h")
	
	install(DIRECTORY Eigen/unsupported/Eigen
		DESTINATION include/gtsam/3rdparty/Eigen/unsupported/
		FILES_MATCHING PATTERN "*.h")
endif()

############ NOTE:  When updating GeographicLib be sure to disable building their examples
############        and unit tests by commenting out their lines:
# add_subdirectory (examples)
# set (TOOLS CartConvert ConicProj GeodesicProj GeoConvert GeodSolve
#   GeoidEval Gravity MagneticField Planimeter TransverseMercatorProj)
# add_subdirectory (tools)

# Find GeographicLib using the find script distributed with it
include(GeographicLib/cmake/FindGeographicLib.cmake)

# Set up the option to install GeographicLib
if(GEOGRAPHICLIB_FOUND)
  set(install_geographiclib_default OFF)
else()
  set(install_geographiclib_default ON)
endif()
option(GTSAM_INSTALL_GEOGRAPHICLIB "Build and install the 3rd-party library GeographicLib" ${install_geographiclib_default})

# Print warning if we'll overwrite GeographicLib
if(GEOGRAPHICLIB_FOUND AND GTSAM_INSTALL_GEOGRAPHICLIB)
  message(WARNING "GeographicLib is installed on your system and GTSAM_INSTALL_GEOGRAPHICLIB is enabled.  Installing gtsam will either overwrite the installed GeographicLib or install a second version that may conflict.  You may want to disable GTSAM_INSTALL_GEOGRAPHICLIB if the installed version was not installed by GTSAM.")
endif()

if(GTSAM_INSTALL_GEOGRAPHICLIB)
  add_subdirectory(GeographicLib)
endif()
