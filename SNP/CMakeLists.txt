
add_subdirectory ("Util")
add_subdirectory ("Storm")

set(MODULE_FILES
	LocalPC.h
	Output.cpp
	Output.h
	SettingsDialog.cpp
	SettingsDialog.h
	SNPModule.cpp
	SNPModule.h
	SNPNetwork.h
	UDPSocket.h
	CriticalSection.cpp
	UI.rc
	caps.mpq
	resource.h
	SNPModule.def
	UDPSocket.cpp
	CMakeLists.txt
	CriticalSection.h
	DirectIP.cpp
	DirectIP.h
	DLLMain.cpp
	LocalPC.cpp
)

add_library (SNP SHARED ${MODULE_FILES})

set(outname "ValveSockets")

set_target_properties (SNP
	PROPERTIES
		OUTPUT_NAME ${outname}
		SUFFIX ".snp"
)

if(WIN32)
  target_link_libraries(SNP wsock32 ws2_32)
endif()


if (CMAKE_VERSION VERSION_GREATER 3.12)
  set_property(TARGET SNP PROPERTY CXX_STANDARD 20)
endif()

target_link_libraries(SNP
	Storm
	Util
)

message(STATUS "${CMAKE_SOURCE_DIR}/SNP/caps.mpq")
message(STATUS "${CMAKE_BINARY_DIR}/SNP/${outname}.snp")

#158kb
#file(READ ${CMAKE_SOURCE_DIR}/SNP/caps.mpq mpqfile)
#message(STATUS "${mpqfile}")
#file(APPEND ${CMAKE_BINARY_DIR}/SNP/${outname}.snp "${mpqfile}")

set( filenames
	"${CMAKE_SOURCE_DIR}/SNP/caps.mpq"
	"${CMAKE_BINARY_DIR}/SNP/${outname}.snp"
)



# $<TARGET_FILE:SNP>
set ( mpqfile "${CMAKE_SOURCE_DIR}/SNP/caps.mpq" )

message(STATUS "${mpqfile}")

add_custom_command( TARGET SNP
	POST_BUILD
	COMMAND cmd /C "copy /Y /B \"$<SHELL_PATH:$<TARGET_FILE:SNP>>\" + \"$<SHELL_PATH:${mpqfile}>\" \"$<SHELL_PATH:$<TARGET_FILE:SNP>>\""
	
)

install(TARGETS SNP
	DESTINATION "C:/Cosmonarchy/Starcraft/"
)


# mklink linked.snp "C:\Users\Jesse\source\repos\SNP\out\build\x86-debug\SNP\ValveSockets.snp