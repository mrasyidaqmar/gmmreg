#=========================================================================
#Program:   Pointset Registration using Gaussian Mixture Model
#Module:    $RCSfile: CMakeLists.txt,v $
#Language:  C++
#Author:    $Author: bjian $
#Date:      $Date: 2008/06/05 18:11:16 $
#Version:   $Revision: 1.2 $
#=========================================================================

CMAKE_MINIMUM_REQUIRED(VERSION 2.4)
IF(COMMAND cmake_policy)
    cmake_policy(SET CMP0003 NEW)
ENDIF(COMMAND cmake_policy)

PROJECT(GMMREG )

# The CMake command FIND_PACKAGE(VXL) attempts to find VXL binary 
# installation.  CMake will look in the directory specified by the 
# CMake variable VXL_DIR.  Normally, CMake will initially not find 
# VXL, will warn you that it could not find VXL, and then give you the
# chance to set the variable VXL_DIR and reconfigure.  VXL_DIR now 
# replaces VXL_BINARY_PATH.

FIND_PACKAGE(VXL)

# Whether FIND_PACKAGE(VXL) worked is stored in the variable 
# VXL_FOUND.  If VXL was found we then include `UseVXL.cmake' 
# which will set many variables prefixed with VXL_ that your project 
# can use to determine what parts of VXL are present and how to use 
# them.  `UseVXL.cmake' holds all the necessary definitions that 
# CMake needs to use VXL.

IF(VXL_FOUND)
  INCLUDE(${VXL_CMAKE_DIR}/UseVXL.cmake)
ENDIF(VXL_FOUND)

# `UseVXL.cmake' will in turn include the file 
# `VXLConfig.cmake' from the top of the VXL build tree.  You 
# should look at this file to see what CMake variables are imported.

# To set VXL_DIR, you could rely on an environment variable: 
# SET( VXL_DIR $ENV{VXLBIN} ) 
# However, you must then make sure that VXLBIN is set correctly every 
# time that cmake is run.  This is often a source of much frustration 
# and is not recommended. 
# 
# Another option is to set the path directly: 
#   SET( VXL_DIR/usr/local/vxl-1.0-beta2/bin ) 
# This has the drawback that the CMakeLists.txt file (this file) has 
# to be modified to suit your particular setup, which may cause 
# trouble.  If this file is under CVS control, for example, you may 
# inadvertently commit it with your local setup hard-coded, thus 
# playing havoc with other users.

# The rest of this CMakeLists.txt file could contain commands as seen 
# in the previous examples, or might just be SUBDIRS commands.


# Remember that all CMake variables defining the VXL configuration 
# begin with "VXL_".  If you want to use VGUI in a client project you
# should use something like this.

#OPTION(BUILD_PYTHON_EXT "Build the Python extension." OFF)
#OPTION(BUILD_MATLAB_MEX "Build the Matlab MEX file." OFF)
OPTION(BUILD_INI_SHARED "Build the port_ini libraries shared." OFF)
OPTION(BUILD_TPS_SHARED "Build the gmmreg_tps libraries shared." OFF)
OPTION(BUILD_CPD_SHARED "Build the gmmreg_cpd libraries shared." OFF)

#OPTION(BUILD_RIGID3D_SHARED "Build the gmmreg_rigid3d libraries shared." OFF)

IF(BUILD_INI_SHARED)
  SET(INI_LIBRARY_TYPE SHARED)
ELSE(BUILD_INI_SHARED)
  SET(INI_LIBRARY_TYPE STATIC)
ENDIF(BUILD_INI_SHARED)

IF(BUILD_CPD_SHARED)
  SET(CPD_LIBRARY_TYPE SHARED)
ELSE(BUILD_CPD_SHARED)
  SET(CPD_LIBRARY_TYPE STATIC)
ENDIF(BUILD_CPD_SHARED)

IF(BUILD_TPS_SHARED)
  SET(TPS_LIBRARY_TYPE SHARED)
ELSE(BUILD_TPS_SHARED)
  SET(TPS_LIBRARY_TYPE STATIC)
ENDIF(BUILD_TPS_SHARED)


IF(WIN32)
  ADD_DEFINITIONS(-DWIN32 -D_CRT_SECURE_NO_DEPRECATE -D_CRT_NONSTDC_NO_DEPRECATE)
ELSE(WIN32)  
	ADD_LIBRARY(port_ini ${INI_LIBRARY_TYPE} port_ini.c)  
ENDIF(WIN32)




SET(GMMREG_L2_TPS_SRCS
         gmmreg_L2_tps_api.cpp
         gmmreg_utils.cpp
         gmmreg_L2_tps_func.cpp
         gmmreg_base.cpp
         gmmreg_L2_tps.cpp
)

SET(GMMREG_CPD_SRCS
         gmmreg_cpd_api.cpp
         gmmreg_utils.cpp
         gmmreg_base.cpp
         gmmreg_cpd.cpp
)


#IF( VXL_VNL_FOUND )
  INCLUDE_DIRECTORIES( ${VXL_VNL_INCLUDE_DIR} )

SET( gauss_transform_sources ${gauss_transform_sources}
test_gauss_transform.cpp gmmreg_utils.cpp
)
ADD_EXECUTABLE( test_gauss_transform ${gauss_transform_sources}
)
#TARGET_LINK_LIBRARIES( test_gauss_transform ${VXL_VNL_LIBRARIES} )
TARGET_LINK_LIBRARIES( test_gauss_transform vnl_algo vnl vcl )

ADD_EXECUTABLE( gmmreg_aux gmmreg_aux.cpp
)
TARGET_LINK_LIBRARIES( gmmreg_aux vnl vnl_algo)


ADD_LIBRARY(gmmreg_L2_tps ${TPS_LIBRARY_TYPE} ${GMMREG_L2_TPS_SRCS})
TARGET_LINK_LIBRARIES(gmmreg_L2_tps vnl_algo vnl vcl)
IF(UNIX)
  TARGET_LINK_LIBRARIES(gmmreg_L2_tps m port_ini)
ENDIF(UNIX)

ADD_LIBRARY(gmmreg_cpd ${CPD_LIBRARY_TYPE} ${GMMREG_CPD_SRCS})
TARGET_LINK_LIBRARIES(gmmreg_cpd vnl_algo vnl vcl)
IF(UNIX)
  TARGET_LINK_LIBRARIES(gmmreg_cpd m port_ini)
ENDIF(UNIX)


  SET( test_gmmreg_L2_tps_sources ${test_gmmreg_L2_tps_sources}
    test_gmmreg_L2_tps.cpp
  )

  SET( test_gmmreg_cpd_sources ${test_gmmreg_cpd_sources}
    test_gmmreg_cpd.cpp
  )

  ADD_EXECUTABLE( test_gmmreg_L2_tps ${test_gmmreg_L2_tps_sources}
  )
  TARGET_LINK_LIBRARIES( test_gmmreg_L2_tps gmmreg_L2_tps )
#  SET(EXECUTABLE_OUTPUT_PATH ../../release)

  ADD_EXECUTABLE( test_gmmreg_cpd ${test_gmmreg_cpd_sources}
  )
  TARGET_LINK_LIBRARIES( test_gmmreg_cpd gmmreg_cpd )

  
  
    
#ENDIF( VXL_VNL_FOUND )


