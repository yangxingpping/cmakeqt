###
# Copyright (c) 2009, Paul Gideon Dann
#
# Permission to use, copy, modify, and/or distribute this software for any
# purpose with or without fee is hereby granted, provided that the above
# copyright notice and this permission notice appear in all copies.
#
# THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES
# WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF
# MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR
# ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES
# WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN
# ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF
# OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
###

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/defines.h.cmake
               ${CMAKE_CURRENT_BINARY_DIR}/defines.h)

file(GLOB_RECURSE UI_FILES *.ui)
file(GLOB_RECURSE CODE_FILES *.cpp)

qt5_wrap_ui(UI_HEADERS ${UI_FILES})
qt5_add_resources(RESOURCE_FILES ../resources/resources.qrc)

# Windows application icon
if (WIN32)
  set(WINDOWS_RES_FILE ${CMAKE_CURRENT_BINARY_DIR}/resources.obj)
  if (MSVC)
    add_custom_command(OUTPUT ${WINDOWS_RES_FILE}
      COMMAND rc.exe /fo ${WINDOWS_RES_FILE} resources.rc
      WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/win
    )
  else()
    add_custom_command(OUTPUT ${WINDOWS_RES_FILE}
      COMMAND windres.exe resources.rc ${WINDOWS_RES_FILE}
      WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/win
    )
  endif()
  
  
    

endif()

add_executable(${CMAKE_PROJECT_NAME} WIN32
  ${UI_HEADERS}
  ${CODE_FILES}
  ${RESOURCE_FILES}
  ${WINDOWS_RES_FILE}
)



target_link_libraries(${CMAKE_PROJECT_NAME}
  Qt5::Widgets
)

if(WIN32)
  add_custom_command(
    TARGET ${CMAKE_PROJECT_NAME} POST_BUILD
    COMMAND  windeployqt $<TARGET_FILE_DIR:${CMAKE_PROJECT_NAME}>
  )
endif()

if (UNIX)
  install(TARGETS ${CMAKE_PROJECT_NAME}
          RUNTIME DESTINATION bin)
elseif (WIN32)
  install(TARGETS ${CMAKE_PROJECT_NAME}
          DESTINATION .)
endif()
