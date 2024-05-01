
add_subdirectory ("Util")
add_subdirectory ("Storm")

	
#SET(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

set(MODULE_FILES
	Storm/storm.h
	Storm/storm.cpp
	Util/MemoryFrame.cpp
	Util/MemoryFrame.h
	Util/Types.h
	Util/Exceptions.h
	Util/Exceptions.cpp
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
	CriticalSection.h
	DirectIP.cpp
	DirectIP.h
	DLLMain.cpp
	signalling.h
	signalling.cpp
	#LocalPC.cpp
	#LocalPC.h
)

add_library (SNP SHARED ${MODULE_FILES})

set(outname "ValveSockets")

set_target_properties (SNP
	PROPERTIES
		OUTPUT_NAME ${outname}
		SUFFIX ".snp"
		VS_DEBUGGER_COMMAND "${SCEXE}"
)
target_include_directories(SNP PUBLIC ${CMAKE_SOURCE_DIR}/SNP/Storm)
target_include_directories(SNP PUBLIC ${CMAKE_SOURCE_DIR}/SNP/Util)
#target_link_libraries(SNP wsock32 ws2_32)


#juice
#target_include_directories(SNP PUBLIC ${CMAKE_SOURCE_DIR}/SNP/include/h)
#add_library(JUICE SHARED IMPORTED)
#set_property(TARGET JUICE PROPERTY
#	IMPORTED_LOCATION "${CMAKE_SOURCE_DIR}/SNP/include/bin/juice.dll"
#)
#set_property(TARGET JUICE PROPERTY
#	IMPORTED_IMPLIB "${CMAKE_SOURCE_DIR}/SNP/include/lib/juice.lib"
#)

if (CMAKE_VERSION VERSION_GREATER 3.12)
  set_property(TARGET SNP PROPERTY CXX_STANDARD 20)
endif()

#target_link_directories(SNP PRIVATE ${CMAKE_SOURCE_DIR}/Juice)



target_include_directories(SNP PUBLIC ${CMAKE_SOURCE_DIR}/include/h)
add_library(STEAMNETWORKINGSOCKETS SHARED IMPORTED)
set_property(TARGET STEAMNETWORKINGSOCKETS PROPERTY
             IMPORTED_IMPLIB "${CMAKE_SOURCE_DIR}/include/bin/GameNetworkingSockets.dll")
set_property(TARGET STEAMNETWORKINGSOCKETS PROPERTY
             IMPORTED_IMPLIB "${CMAKE_SOURCE_DIR}/include/lib/GameNetworkingSockets.lib")

target_link_libraries(SNP
	PRIVATE STEAMNETWORKINGSOCKETS
	PUBLIC wsock32
	PUBLIC ws2_32
)

message(STATUS "${CMAKE_SOURCE_DIR}/SNP/caps.mpq")
message(STATUS "${CMAKE_BINARY_DIR}/SNP/${outname}.snp")


set( filenames
	"${CMAKE_SOURCE_DIR}/SNP/caps.mpq"
	"${CMAKE_BINARY_DIR}/SNP/${outname}.snp"
)


set ( mpqfile "${CMAKE_SOURCE_DIR}/SNP/caps.mpq" )

add_custom_command( TARGET SNP
	POST_BUILD
	COMMAND cmd /C "copy /Y /B \"$<SHELL_PATH:$<TARGET_FILE:SNP>>\" + \"$<SHELL_PATH:${mpqfile}>\" \"$<SHELL_PATH:$<TARGET_FILE:SNP>>\""
	
)

install(TARGETS SNP
	DESTINATION "C:/Cosmonarchy/Starcraft/"
)



# mklink linked.snp "C:\Users\Jesse\source\repos\SNP\out\build\x86-debug\SNP\ValveSockets.snp