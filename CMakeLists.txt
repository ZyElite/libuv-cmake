cmake_minimum_required(VERSION 2.8.4 FATAL_ERROR)
set(CMAKE_LEGACY_CYGWIN_WIN32 0) # Remove when CMake >= 2.8.4 is required

project(uv)

set(LIBUVDIR ${PROJECT_SOURCE_DIR}/libuv)

include_directories(${LIBUVDIR}/include ${LIBUVDIR}/src)

file(GLOB SOURCES "${LIBUVDIR}/src/*.c")

if(WIN32)
  add_definitions(-DWIN32_LEAN_AND_MEAN -D_WIN32_WINNT=0x0600 -D_CRT_SECURE_NO_WARNINGS)
  include_directories(${LIBUVDIR}/src/win)
  file(GLOB WIN_SOURCES "${LIBUVDIR}/src/win/*.c")
  set(SOURCES ${SOURCES} ${WIN_SOURCES})
else()
  include_directories(${LIBUVDIR}/src/unix)
  file(GLOB UNIX_SOURCES "${LIBUVDIR}/src/unix/*.c")
  set(SOURCES ${SOURCES} ${UNIX_SOURCES})
endif()

message(STATUS "print sources.")
foreach(f ${SOURCES})
    message(STATUS "\\- ${f}")
endforeach()

add_library(uv ${SOURCES})

