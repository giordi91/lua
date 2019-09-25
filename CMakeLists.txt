cmake_minimum_required(VERSION 3.0.0)

project(lualib)   

    #looking for  files
    file(GLOB HEADER_FILES  "*.h")
	SET(SOURCE_FILES 
	lapi.c 
	lcode.c
	lctype.c 
	ldebug.c 
	ldo.c 
	ldump.c 
	lfunc.c 
	lgc.c 
	llex.c 
	lmem.c 
	lobject.c 
	lopcodes.c 
	lparser.c 
	lstate.c 
	lstring.c 
	ltable.c 
	ltm.c 
	lundump.c 
	lvm.c 
	lzio.c 
	#ltests.o
	)

    include_directories(${CMAKE_CURRENT_SOURCE_DIR} )

	#making sure to add the common cpp flags, that are defined in the main cpp file
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${COMMON_CXX_FLAGS}")

    #adding the lib 
    add_library(${PROJECT_NAME} STATIC ${SOURCE_FILES} ${INCLUDES_FILES})

	target_include_directories(${PROJECT_NAME} PUBLIC ${HEADER_FILES})

	#setting the plug-in folder in the solution
	#set_target_properties (${PROJECT_NAME} PROPERTIES
    #FOLDER vendors) 
	
	if(APPLE)
			MESSAGE(STATUS "Using MACOS")
	elseif(UNIX)
		set(LUA_DEFINITIONS LUA_USE_POSIX)
		target_compile_definitions(${PROJECT_NAME} PUBLIC ${LUA_DEFINITIONS})
	elseif(WIN32)
		#copying built lib to the output
		add_custom_command(
		TARGET ${PROJECT_NAME} 
		POST_BUILD
		COMMAND ${CMAKE_COMMAND} -E copy
		  $<TARGET_FILE:${PROJECT_NAME}>
		  ${CMAKE_SOURCE_DIR}/builtDependencies/windows/$<CONFIGURATION>/$<TARGET_FILE_NAME:${PROJECT_NAME}>)		
	endif()

	#copying the headers
	file(COPY ${HEADER_FILES} DESTINATION ${CMAKE_SOURCE_DIR}/builtDependencies/includes/lua )
