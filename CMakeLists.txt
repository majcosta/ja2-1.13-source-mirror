cmake_minimum_required(VERSION 3.12.0)

project(ja2)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

if(MSVC)

	# Replace /MD compiler flag with /MT
	set(CompilerFlags
        CMAKE_CXX_FLAGS
        CMAKE_CXX_FLAGS_DEBUG
        CMAKE_CXX_FLAGS_RELEASE
        CMAKE_C_FLAGS
        CMAKE_C_FLAGS_DEBUG
        CMAKE_C_FLAGS_RELEASE
        )
	foreach(CompilerFlag ${CompilerFlags})
		string(REPLACE "/MD" "/MT" ${CompilerFlag} "${${CompilerFlag}}")
	endforeach()
	
	# Compiler and linker flags for debug/release
	set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /JMC /GS /analyze- /Gm- /Zc:inline /fp:precise /Zc:forScope /Oy- /FC /nologo")
	set(CMAKE_EXE_LINKER_FLAGS_DEBUG "${CMAKE_EXE_LINKER_FLAGS_DEBUG} /NXCOMPAT /SAFESEH:NO")

	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /GS /analyze- /Gy /Zc:wchar_t /Zc:inline /fp:precise /Zc:forScope /Oy- /Oi /FC /nologo")	
	set(CMAKE_EXE_LINKER_FLAGS_RELEASE "${CMAKE_EXE_LINKER_FLAGS_RELEASE} /NXCOMPAT /OPT:REF /SAFESEH:NO /OPT:ICF")
endif()

add_compile_definitions(JA2 CINTERFACE XML_STATIC VFS_STATIC VFS_WITH_SLF VFS_WITH_7ZIP USE_VFS ENGLISH)

if(CMAKE_BUILD_TYPE MATCHES Debug) # debug specific preprocessor flags
	add_compile_definitions(JA2BETAVERSION JA2TESTVERSION DEBUG_ATTACKBUSY)
#elseif(CMAKE_BUILD_TYPE MATCHES Release) # release specific preprocessor flags would go here but I couldn't find any
endif()

if(JA2EDITOR MATCHES ON)
	add_compile_definitions(JA2EDITOR)
	set(JA2_EXE_NAME "MapEditor")
else()
	set(JA2_EXE_NAME "Ja2")
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

add_executable(${JA2_EXE_NAME} WIN32 "Credits.cpp"
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

target_link_libraries(${JA2_EXE_NAME}
"Winmm.lib"
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
)

### Apparently this doesn't build

#add_executable(ja2export WIN32 "${ja2export_SOURCE_DIR}/main.cpp" "${ja2export_SOURCE_DIR}/init_vfs.cpp" "${ja2export_SOURCE_DIR}/progress_bar.cpp")
#target_include_directories(ja2export PRIVATE "ext/export/src" "ext/export/src/ja2")
#target_link_libraries(ja2export bfVFS 7z libpng zlib)


# If uncommented, lists all known CMake variables at this point - Useful for debugging
#get_cmake_property(_variableNames VARIABLES)
#list(SORT _variableNames)
#foreach (_variableName ${_variableNames})
#    message(STATUS "${_variableName}=${${_variableName}}")
#endforeach()