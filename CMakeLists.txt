cmake_minimum_required(VERSION 2.8.4 FATAL_ERROR)
set(CMAKE_LEGACY_CYGWIN_WIN32 0) # Remove when CMake >= 2.8.4 is required

project(uv)

set(LIBUVDIR ${PROJECT_SOURCE_DIR}/libuv)

message(STATUS "Libuv directory: ${LIBUVDIR}")

include_directories(${LIBUVDIR}/include ${LIBUVDIR}/src)

file(GLOB SOURCES "${LIBUVDIR}/src/*.c")

IF(NOT CYGWIN)
  if(WIN32)
    add_definitions(-DWIN32_LEAN_AND_MEAN -D_WIN32_WINNT=0x0600 -D_CRT_SECURE_NO_WARNINGS)
    include_directories(${LIBUVDIR}/src/win)
    file(GLOB WIN_SOURCES "${LIBUVDIR}/src/win/*.c")
    set(SOURCES ${SOURCES} ${WIN_SOURCES})
  elseif(MINGW)
    add_definitions(-DWIN32_LEAN_AND_MEAN -D_WIN32_WINNT=0x0600)
    include_directories(${LIBUVDIR}/src/win)
    set(WIN_SOURCES
      ${LIBUVDIR}/include/stdint-msvc2008.h
      ${LIBUVDIR}/include/tree.h
      ${LIBUVDIR}/include/uv-errno.h
      ${LIBUVDIR}/include/uv-threadpool.h
      ${LIBUVDIR}/include/uv-version.h
      ${LIBUVDIR}/include/uv-win.h
      ${LIBUVDIR}/include/uv.h
      ${LIBUVDIR}/src/heap-inl.h
      ${LIBUVDIR}/src/quene.h
      ${LIBUVDIR}/src/uv-common.h
      ${LIBUVDIR}/src/win/atomicops-inl.h
      ${LIBUVDIR}/src/win/handle-inl.h
      ${LIBUVDIR}/src/win/internal.h
      ${LIBUVDIR}/src/win/req-inl.h
      ${LIBUVDIR}/src/win/stream-inl.h
      ${LIBUVDIR}/src/win/winsock.h
      ${LIBUVDIR}/src/fs-poll.c
      ${LIBUVDIR}/src/inet.c
      ${LIBUVDIR}/src/threadpool.c
      ${LIBUVDIR}/src/uv-common.c
      ${LIBUVDIR}/src/version.c
      ${LIBUVDIR}/src/win/async.c
      ${LIBUVDIR}/src/win/core.c
      ${LIBUVDIR}/src/win/dl.c
      ${LIBUVDIR}/src/win/error.c
      ${LIBUVDIR}/src/win/fs-event.c
      ${LIBUVDIR}/src/win/fs.c
      ${LIBUVDIR}/src/win/getaddrinfo.c
      ${LIBUVDIR}/src/win/getnameinfo.c
      ${LIBUVDIR}/src/win/handle.c
      ${LIBUVDIR}/src/win/loop-watcher.c
      ${LIBUVDIR}/src/win/pipe.c
      ${LIBUVDIR}/src/win/poll.c
      ${LIBUVDIR}/src/win/process-stdio.c
      ${LIBUVDIR}/src/win/process.c
      ${LIBUVDIR}/src/win/req.c
      ${LIBUVDIR}/src/win/signal.c
      ${LIBUVDIR}/src/win/stream.c
      ${LIBUVDIR}/src/win/tcp.c
      ${LIBUVDIR}/src/win/thread.c
      ${LIBUVDIR}/src/win/timer.c
      ${LIBUVDIR}/src/win/tty.c
      ${LIBUVDIR}/src/win/udp.c
      ${LIBUVDIR}/src/win/util.c
      ${LIBUVDIR}/src/win/winapi.c
      ${LIBUVDIR}/src/win/winsock.c
    )
    set(SOURCES ${WIN_SOURCES})
  else()
    include_directories(${LIBUVDIR}/src/unix)

    if(APPLE)
      add_definitions("-D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64 -D_DARWIN_USE_64_BIT_INODE=1 -D_DARWIN_UNLIMITED_SELECT=1")

      if(NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
        add_definitions("-DNDEBUG")
        set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g -std=gnu89 -pedantic -fstrict-aliasing  -Wall -Wextra -Wno-unused-parameter -Wendif-labels")
      else()
        add_definitions("-DDEBUG -D_DEBUG -DEV_VERIFY=2")
        set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g -std=gnu89 -pedantic -fstrict-aliasing  -Wall -Wextra -Wno-unused-parameter -Wendif-labels -Wno-strict-aliasing")
      endif()
      set(UNIX_SOURCES
        ${LIBUVDIR}/src/unix/async.c
        ${LIBUVDIR}/src/unix/core.c
        ${LIBUVDIR}/src/unix/darwin-proctitle.c
        ${LIBUVDIR}/src/unix/darwin.c
        ${LIBUVDIR}/src/unix/dl.c
        ${LIBUVDIR}/src/unix/fs.c
        ${LIBUVDIR}/src/unix/fsevents.c
        ${LIBUVDIR}/src/unix/getaddrinfo.c
        ${LIBUVDIR}/src/unix/getnameinfo.c
        ${LIBUVDIR}/src/unix/kqueue.c
        ${LIBUVDIR}/src/unix/loop-watcher.c
        ${LIBUVDIR}/src/unix/loop.c
        ${LIBUVDIR}/src/unix/pipe.c
        ${LIBUVDIR}/src/unix/poll.c
        ${LIBUVDIR}/src/unix/process.c
        ${LIBUVDIR}/src/unix/proctitle.c
        ${LIBUVDIR}/src/unix/signal.c
        ${LIBUVDIR}/src/unix/spinlock.h
        ${LIBUVDIR}/src/unix/stream.c
        ${LIBUVDIR}/src/unix/tcp.c
        ${LIBUVDIR}/src/unix/thread.c
        ${LIBUVDIR}/src/unix/timer.c
        ${LIBUVDIR}/src/unix/tty.c
        ${LIBUVDIR}/src/unix/udp.c
      )
    else()
      # Linux
      add_definitions("-D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64 -D_GNU_SOURCE")

      if(NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
        add_definitions("-DNDEBUG")
        set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -pthread -Wall -ansi -fvisibility=hidden -g -std=gnu89 -pedantic -Wextra -Wno-unused-parameter -Wstrict-aliasing -fomit-frame-pointer -fdata-sections -ffunction-sections")
      else()
        add_definitions("-DDEBUG -D_DEBUG -DEV_VERIFY=2")
        set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -pthread -Wall -ansi -fvisibility=hidden -g -std=gnu89 -pedantic -Wextra -Wno-unused-parameter -Wstrict-aliasing -fwrapv")
      endif()

      set(UNIX_SOURCES
        ${LIBUVDIR}/src/unix/async.c
        ${LIBUVDIR}/src/unix/core.c
        ${LIBUVDIR}/src/unix/dl.c
        ${LIBUVDIR}/src/unix/fs.c
        ${LIBUVDIR}/src/unix/getaddrinfo.c
        ${LIBUVDIR}/src/unix/getnameinfo.c
        ${LIBUVDIR}/src/unix/loop.c
        ${LIBUVDIR}/src/unix/loop-watcher.c
        ${LIBUVDIR}/src/unix/pipe.c
        ${LIBUVDIR}/src/unix/poll.c
        ${LIBUVDIR}/src/unix/process.c
        ${LIBUVDIR}/src/unix/signal.c
        ${LIBUVDIR}/src/unix/stream.c
        ${LIBUVDIR}/src/unix/tcp.c
        ${LIBUVDIR}/src/unix/thread.c
        ${LIBUVDIR}/src/unix/timer.c
        ${LIBUVDIR}/src/unix/tty.c
        ${LIBUVDIR}/src/unix/udp.c
        ${LIBUVDIR}/src/unix/proctitle.c
        ${LIBUVDIR}/src/unix/linux-core.c
        ${LIBUVDIR}/src/unix/linux-inotify.c
        ${LIBUVDIR}/src/unix/linux-syscalls.c
      )
    endif()
    set(SOURCES ${SOURCES} ${UNIX_SOURCES})
  endif()
ENDIF()

message(STATUS "print sources.")
foreach(f ${SOURCES})
    message(STATUS "\\- ${f}")
endforeach()

add_library(uv ${SOURCES})

# vim:sw=2 ts=2 expandtab
