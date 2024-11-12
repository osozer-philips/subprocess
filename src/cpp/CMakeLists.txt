file(GLOB src ./subprocess/*.cpp)

find_package(Threads REQUIRED)

add_library(subprocess SHARED ${src})
target_include_directories(subprocess PUBLIC
    ./
)

if(MSVC)
    target_compile_options(subprocess PUBLIC -Zc:__cplusplus)
endif()

if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    target_link_libraries(subprocess PUBLIC stdc++fs)
    if(NOT WIN32)
        target_link_libraries(subprocess PUBLIC dl)
    endif()
endif()

target_link_libraries(subprocess PUBLIC Threads::Threads)

set_target_properties(subprocess PROPERTIES VERSION "${CMAKE_PROJECT_VERSION}" SOVERSION 0)

install(
  TARGETS subprocess
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib)

install(
  DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/"
  DESTINATION "include"
  FILES_MATCHING
  PATTERN "*.hpp")

include(GNUInstallDirs)
set(pkgconfig "${CMAKE_BINARY_DIR}/${CMAKE_PROJECT_NAME}.pc")
configure_file("${PROJECT_SOURCE_DIR}/pkgconfig.pc.in" "${pkgconfig}" @ONLY)
install(FILES "${pkgconfig}" DESTINATION "${CMAKE_INSTALL_LIBDIR}/pkgconfig")
