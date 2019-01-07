cmake_minimum_required(VERSION 3.12.0)

project(ja2)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

if(MSVC)	
	set(CompilerFlags # Replace /MD compiler flag with /MT
        CMAKE_CXX_FLAGS
        CMAKE_CXX_FLAGS_DEBUG
        CMAKE_CXX_FLAGS_RELEASE
		CMAKE_CXX_FLAGS_RELWITHDEBINFO
        CMAKE_C_FLAGS
        CMAKE_C_FLAGS_DEBUG
        CMAKE_C_FLAGS_RELEASE
		CMAKE_C_FLAGS_RELWITHDEBINFO
        )
	foreach(CompilerFlag ${CompilerFlags})
		string(REPLACE "/MD" "/MT" ${CompilerFlag} "${${CompilerFlag}}")
	endforeach()
	
	# Compiler flags for debug/release based on VS2017 sln/vcprojx files
	set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /Zc:inline /Oy- /FC")

	set(JA2_RELEASE_CXX_FLAGS "/Gy /Zc:inline /Oi /FC") # common to both Release and RelWithDebInfo

	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} ${JA2_RELEASE_CXX_FLAGS}")
	set(CMAKE_CXX_FLAGS_RELWITHDEBINFO "${CMAKE_CXX_FLAGS_RELWITHDEBINFO} ${JA2_RELEASE_CXX_FLAGS}")
	
	if(JA2EDITOR MATCHES ON) # This variable is passed to CMake (or isn't) by the CMakeSettings.json scheme in the "cmakeCommandArgs parameter of the MapEditor configurations
		add_compile_definitions(JA2EDITOR)
		set(JA2_TARGET_NAME "MapEditor")
		add_compile_definitions(JA2BETAVERSION)
	else()
		set(JA2_TARGET_NAME "Ja2")
	endif()
endif()

add_compile_definitions(JA2 CINTERFACE XML_STATIC USE_VFS VFS_STATIC VFS_WITH_SLF VFS_WITH_7ZIP ENGLISH)

if(CMAKE_BUILD_TYPE MATCHES Debug) # debug specific preprocessor flags
	add_compile_definitions(JA2BETAVERSION JA2TESTVERSION DEBUG_ATTACKBUSY)
endif()

include_directories("Standard Gaming Platform" 
	"ext/vfs/include"
	"utils"
	"Tactical"
	"Laptop"
	"."
	"Strategic"
	"TileEngine"
	"Multiplayer"
	"Editor"
	"TacticalAI"
	"Res"
	"lua"
	"Multiplayer/raknet"
	"Console"
	"ext/libpng"
	"ModularizedTacticalAI/include")

add_subdirectory("ext/zlib")
add_subdirectory("ext/VFS")
add_subdirectory("TileEngine")
add_subdirectory("Utils")
add_subdirectory("Strategic")
add_subdirectory("Editor")
add_subdirectory("Standard Gaming Platform")
add_subdirectory("ext/libpng")
add_subdirectory("lua")
add_subdirectory("TacticalAI")
add_subdirectory("Laptop")
add_subdirectory("Console")
add_subdirectory("Tactical")
add_subdirectory("ModularizedTacticalAI/src")
add_subdirectory("ext/export")

set(JA2_SOURCES "Credits.cpp"
	"Fade Screen.cpp"
	"GameInitOptionsScreen.cpp"
	"GameSettings.cpp"
	"GameVersion.cpp"
	"HelpScreen.cpp"
	"Init.cpp"
	"Intro.cpp"
	"JA2 Splash.cpp"
	"Ja25Update.cpp"
	"Language Defines.cpp"
	"Loading Screen.cpp"
	"MPChatScreen.cpp"
	"MPConnectScreen.cpp"
	"MPHostScreen.cpp"
	"MPJoinScreen.cpp"
	"MPScoreScreen.cpp"
	"MPXmlTeams.cpp"
	"MainMenuScreen.cpp"
	"MessageBoxScreen.cpp"
	"Options Screen.cpp"
	"SaveLoadGame.cpp"
	"SaveLoadScreen.cpp"
	"Screens.cpp"
	"Sys Globals.cpp"
	"XML_DifficultySettings.cpp"
	"XML_IntroFiles.cpp"
	"XML_Layout_MainMenu.cpp"
	"aniviewscreen.cpp"
	"gameloop.cpp"
	"gamescreen.cpp"
	"jascreens.cpp"
	"legion cfg.cpp"
	"profiler.cpp"
	"Multiplayer/client.cpp"
	"Multiplayer/server.cpp"
	"Multiplayer/transfer_rules.cpp"
	"Res/ja2.rc")

set(JA2_LIBRARIES "Winmm.lib"
	"${PROJECT_SOURCE_DIR}/Multiplayer/raknet/RakNetLibStatic.lib"
	"ws2_32.lib"
	"DbgHelp.lib"
	Console
	Editor
	Laptop
	lua
	SGP
	Strategic
	Tactical
	TacticalAI
	ModularizedTacticalAI
	TileEngine
	Utils
	bfVFS
	7z
	libpng
	zlib
	"${PROJECT_SOURCE_DIR}/libexpatMT.lib"
	"${PROJECT_SOURCE_DIR}/mss32.lib"
	"${PROJECT_SOURCE_DIR}/SMACKW32.lib"
	"${PROJECT_SOURCE_DIR}/binkw32.lib"
	"${PROJECT_SOURCE_DIR}/fmodvc.lib"
	"${PROJECT_SOURCE_DIR}/lua51.lib"
	"${PROJECT_SOURCE_DIR}/VtuneApi.lib"
	"${PROJECT_SOURCE_DIR}/Standard Gaming Platform/ddraw.lib"
	"odbc32.lib" 
	"odbccp32.lib"
#	"legacy_stdio_definitions.lib"
	)

add_executable(${JA2_TARGET_NAME} WIN32 ${JA2_SOURCES})
add_dependencies(${JA2_TARGET_NAME} 7z Console Editor Laptop libpng lua ModularizedTacticalAI SGP Strategic Tactical TacticalAI TileEngine Utils bfVFS zlib)
target_link_libraries(${JA2_TARGET_NAME} ${JA2_LIBRARIES})

# If uncommented, lists all known CMake variables at this point - Useful for debugging
#get_cmake_property(_variableNames VARIABLES)
#list(SORT _variableNames)
#foreach (_variableName ${_variableNames})
#    message(STATUS "${_variableName}=${${_variableName}}")
#endforeach()
