include_directories(${OSG_INCLUDES})

# Source files
SET( srcs
        daOsgHEngine.cpp
        )

#######################################################################################################################
# Headers
SET( headers
	../../HAPI_CPP.h
        )

#######################################################################################################################
# Setup compile info

add_executable(daOsgHEngine ${srcs} ${headers})
target_link_libraries(daOsgHEngine
	omega
	omegaToolkit
	omegaOsg
	cyclops
	HAPI
)

set_target_properties(daOsgHEngine PROPERTIES PREFIX "")

# # need to add some compiler flags
IF( CMAKE_SYSTEM_PROCESSOR STREQUAL "x86_64" )
  SET_TARGET_PROPERTIES(daOsgHEngine PROPERTIES COMPILE_FLAGS "-fPIC -m64")
	set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -Bsymbolic")
	set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -Bsymbolic")
ENDIF( CMAKE_SYSTEM_PROCESSOR STREQUAL "x86_64" )
