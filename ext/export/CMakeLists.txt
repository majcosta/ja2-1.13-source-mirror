add_executable(ja2export 
"src/init_vfs.cpp" 
"src/main.cpp" 
"src/progress_bar.cpp" 
"src/ja2/XMLWriter.cpp" 
"src/ja2/himage.cpp" 
"src/export/jsd/export_jsd.cpp" 
"src/export/jsd/structure.cpp" 
"src/export/slf/export_slf.cpp" 
"src/export/sti/export_sti.cpp" 
"src/export/sti/Image.cpp" 
"src/export/sti/stci_image_utils.cpp" 
"src/export/sti/STCI_lib.cpp")
target_include_directories(ja2export PUBLIC "../VFS/include" "../libpng" "../zlib" "src" "src/ja2")
target_link_libraries(ja2export bfVFS 7z libpng zlib)