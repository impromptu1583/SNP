

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


install(
	TARGETS SNP
	COMPONENT Libraries)

install(
    FILES DLLMain.cpp
    DESTINATION SNP
    COMPONENT Headers)