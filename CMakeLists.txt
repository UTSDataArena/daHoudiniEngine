#################################################################################
# Houdini Engine Module for Omegalib
#
# Authors:
#   Darren Lee             darren.lee@uts.edu.au
#
# Copyright 2015-2016,     Data Arena, University of Technology Sydney
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
#
# 1. Redistributions of source code must retain the above copyright notice, this
#    list of conditions and authors, and the following disclaimer.
# 2. Redistributions in binary form must reproduce the above copyright notice,
#    this list of conditions and the following disclaimer in the documentation
#    and/or other materials provided with the distribution.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
# ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
# WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
# DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR
# ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
# (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
# LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND
# ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
# SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
#
# The views and conclusions contained in the software and documentation are those
# of the authors and should not be interpreted as representing official policies,
# either expressed or implied, of the Data Arena Project.
#
#--------------------------------------------------------------------------------
#
# Based on examples from the Omegalib Project
#
#################################################################################


request_dependency(cyclops)

#directory for HAPI, reads the $HFS variable
set(HOUDINI_BASE $ENV{HFS} CACHE PATH "Set to Houdini base directory (/opt/hfs.x.y.z)")

# if the base exists, compile against HEngine
if (HOUDINI_BASE)
	set(DA_ENABLE_HENGINE true CACHE BOOL "Enables compiling against Houdini Engine")
	SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DDA_ENABLE_HENGINE")
else()
	message(WARNING "Could not find Houdini base directory. Set this manually and enable 'DA_ENABLE_HENGINE'")
endif()

set(MODULE_NAME daHEngine)

if (DA_ENABLE_HENGINE)
	include_directories(${HOUDINI_BASE}/toolkit/include)
	include_directories(.)
	include_directories(${OSG_INCLUDES})
	link_directories(${HOUDINI_BASE}/dsolib)
else()
	include_directories(.)
	include_directories(${OSG_INCLUDES})
endif()

link_directories(./)

set (SRCS 
	houdiniAsset.cpp
	houdiniGeometry.cpp
	houdiniParameter.cpp
	daHEngine.cpp
	loaderTools.cpp
	daPly/ReaderWriterPLY.cpp
	daPly/vertexData.cpp
	daPly/plyfile.cpp
)

set ( LIBS
	omega
	omegaToolkit
	omegaOsg
)

if (DA_ENABLE_HENGINE)
	# add_subdirectory(examples/daOsgHEngine)
	# add_subdirectory(examples/daHoudiniEngine)
	set (SRCS 
		${SRCS}
		houdiniUiParm.cpp
		daHEngine.event.cpp
		daHEngine.parm.cpp
		daHEngine.processAsset.cpp
		daHEngine.sharedData.cpp
		daHEngine.util.cpp
	)
	set ( LIBS
		${LIBS}
		HAPIL
	)
endif()

add_library(${MODULE_NAME} MODULE ${SRCS} )
target_link_libraries(${MODULE_NAME} ${LIBS} )

# # need to add some compiler flags
IF( CMAKE_SYSTEM_PROCESSOR STREQUAL "x86_64" )
  SET_TARGET_PROPERTIES(${MODULE_NAME} PROPERTIES COMPILE_FLAGS "-fPIC -m64")
	set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -Bsymbolic")
	set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -Bsymbolic")
ENDIF( CMAKE_SYSTEM_PROCESSOR STREQUAL "x86_64" )

#------------------------------------------------------------------------------
# DO NOT MODIFY ANYTHING BELOW AFTER THIS LINE
set_target_properties(${MODULE_NAME} PROPERTIES PREFIX "")
if(WIN32)
	set_target_properties(${MODULE_NAME} PROPERTIES FOLDER modules SUFFIX ".pyd")
endif()
